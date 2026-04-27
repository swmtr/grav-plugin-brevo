# Brevo Plugin for Grav CMS

The **Brevo** plugin adds [Brevo](https://www.brevo.com) (formerly Sendinblue) contact list subscription as a form process action for [Grav CMS](https://getgrav.org).

When a Grav form is submitted with the `brevo` process action, the plugin calls the Brevo contacts API server-side to add the contact to the specified list(s) with any mapped attributes.

## Demo
See the bottom of the article - [Join the Waitlist](https://360swim.com/blog/warm-down-your-way-to-recovery)

## Requirements

- Grav 1.7+
- Form plugin 7.0+
- PHP 7.4+
- curl extension enabled

## Installation

### Manual

Download or clone this repository and copy the `brevo` folder to `user/plugins/brevo` on your Grav installation.

### GPM (coming soon)

```
bin/gpm install brevo
```

## Configuration

Copy `user/plugins/brevo/brevo.yaml` to `user/config/plugins/brevo.yaml` and add your Brevo API key:

```yaml
enabled: true
api_key: 'YOUR_BREVO_V3_API_KEY'
default_list_id: ''
```

**Getting your API key:** In Brevo, go to **Account → SMTP & API → API Keys** and generate a new key.

You can also configure the plugin via the Grav Admin panel under **Plugins → Brevo**.

## Usage

Add `brevo` as a process action in your Grav form frontmatter:

```yaml
form:
  name: newsletter
  fields:
    - name: email
      label: Email
      type: email
      validate:
        required: true
    - name: first_name
      label: First Name
      type: text

  buttons:
    - type: submit
      value: Subscribe

  process:
    - brevo:
        lists: [3]
        field_mappings:
          FIRSTNAME: first_name
```

### Finding your list ID

In Brevo, go to **Contacts → Lists**. Each list displays its ID number — use that number in the `lists` array. You can add a contact to multiple lists at once:

```yaml
lists: [3, 7]
```

### Options

| Option | Required | Description |
|--------|----------|-------------|
| `lists` | Recommended | Array of Brevo list IDs to add the contact to. Falls back to `default_list_id` in plugin config if omitted. |
| `field_mappings` | Optional | Maps Brevo contact attributes (key) to Grav form field names (value). |

### Field mappings

`field_mappings` lets you pass custom contact attributes to Brevo. The key is the Brevo attribute name as defined in your Brevo audience (**Contacts → Settings → Contact attributes**), the value is the Grav form field name.

```yaml
field_mappings:
  FIRSTNAME: first_name
  LASTNAME: last_name
  CUSTOMATTR: my_custom_field
```

You need to create the attributes in Brevo before they can be used. Go to **Contacts → Settings → Contact attributes** and add any custom attributes you want to store.

## How it works

On form submission, the plugin:

1. Reads the `email` field from the submitted form
2. Reads any mapped fields and builds the attributes object
3. POSTs to `https://api.brevo.com/v3/contacts` with your API key
4. Logs errors to Grav's log if the API call fails

The `updateEnabled` flag is set to `true` — if the contact already exists in Brevo, their attributes and list membership will be updated rather than returning an error.

## Error handling

Errors are logged to `logs/grav.log`. Check there if contacts are not appearing in Brevo after form submission.

Common issues:
- **API key not set** — check `user/config/plugins/brevo.yaml`
- **List ID not found** — verify the list ID exists in your Brevo account under **Contacts → Lists**
- **Attribute not found** — make sure the attribute is created in Brevo under **Contacts → Settings → Contact attributes** before using it in `field_mappings`

## See it in action

This plugin is used on [360swim.com](https://360swim.com) to power the AI swim coaching tool waitlist. You can see a live example of a Brevo subscription form embedded in a Grav blog post on any technique article at [360swim.com/blog](https://360swim.com/blog).

## License

MIT — see [LICENSE](LICENSE)

If you found this guide useful, why not let it be known by [sending me a few sats](https://360swim.com/ln-donate-github) or via LN address⚡swmtr@360swim.com .
<br />
<img src="https://360swim.com/user/themes/swimquark/images/ln_git.png" width="400" />
 
Finally, if you are into swimming, checkout some [swimming tips](https://360swim.com/tips).
