# Hugo Module for everything server

## Redirect Headers

We call redirects anything that takes an intial URL to match, the source, and redirect to another destination, the target.
The module processes a list of rules, defined by the user through either the project's configuration file or a returning partials.

### API

Any given rule properties are defined by the following keys. Those marked by an * are mandatory

__source__\*: the URL to match
__target__\*: the destination
__code__: the status code (default: 301)
__country__: the country if redirection is subject to a country code
__language__: the language if redirection is subject to a language code
__role__: the user role if redirection is subject to a user role

### Configuration

Configuration is set through the site's configurationf file's using the `tnd_server` reserved keys.

__use_aliases__: Should the module build redirect rules for entries using Hugo's reserved `aliases` Front Matter key. 
If so, you should disable Hugo's own Aliases feature with `disableAliases: true`
__redirects__: A list of custom redirect rules. See below.

### Custom Redirect Headers

#### Configuration File (hardcoded)
User can add a list of redirects through the site's configuration file. Each rule can use the API's available keys.

```yaml
# config.yaml
params:
  tnd_server:
    redirects:
      - source: /something
        target: /something-else
      - source: /contact
        target: /fr/contact
        language: fr
```

#### Returning Partial (dynamnic)

In order to add dynamic redirect rules built by Hugo, user can create a dedicated returning patial in the project at `layouts/partials/server/AddRedirects.html`. 
The partial should return a slice of maps which uses the API's available keys.

##### Example

Following code add redirect rules for some blog posts so Canadian visitor are redirected to a dedicated page.

```
{{/*
  AddRedirects
  
  In this project, every posts with `.Params.redirect_ca` set to true should redirect
  canadian visitors to a dedicated information page.

  @author @user

  @context Any (.)

  @access public

*/}}

{{/* We create a returning variable */}}
{{ $redirs := slice }}

{{ $posts := where site.RegularPages "Params.redirect_ca" "==" true }}

{{ range $posts }}
  {{/* We create a map for a rule with source, target and country key*/}}
  {{ $rule := dict "source" .RelPermalink "target" "/canadian-regulation" "country" "ca" }}

  {{/* We add the rule's map to the $redirs slice */}}
  {{ $redirs = $redirs | append $rule }}
{{ end }}

{{ return $redirs }}
```