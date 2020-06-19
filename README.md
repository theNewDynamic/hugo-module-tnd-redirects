# TND Redirects.

This modules helps create and maintain redirections for your Hugo project. It's designed to scope to multiple services, but only caters for Netlify just now.

## What are redirections?

We call redirection anything that takes an initial URL to match (the _source_), and redirects to another destination, (the _target_).
The module processes a list of rules, defined by the user through either the project's configuration file or a returning partials and make sure their correctly processed by a given hosting service.

## API

Any given rule properties are defined by the following keys. Those marked by an * are mandatory

- __source__\*: the URL to match
- __target__\*: the destination
- __code__: the status code (default: 301)
- __force__: Is it a force redirect? (default: false)
- __country__: the country if redirection is subject to a country code
- __language__: the language if redirection is subject to a language code
- __role__: the user role if redirection is subject to a user role

## Configuration

Configuration is set through the site's configurationf file's using the `tnd_redirects` reserved keys.

__use_aliases__: Should the module build redirect rules for entries using Hugo's reserved `aliases` Front Matter key. 
If so, you should disable Hugo's own Aliases feature with `disableAliases: true`
__redirects__: A list of custom redirect rules. See below.

## Adding Custom Redirects

### Though Configuration File (hardcoded)
User can add a list of redirects through the site's configuration file. Each rule can use the API's available keys.

```yaml
# config.yaml
params:
  tnd_redirects:
    redirects:
      - source: /something
        target: /something-else
      - source: /contact
        target: /fr/contact
        language: fr
```

### Through a Returning Partial (dynamnic)

In order to add dynamic redirect rules built by Hugo, user can create a dedicated returning partial in the project at `layouts/partials/tnd-redirects/AddRedirects.html`. 
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

## Who dat?

This module is created, maintained and loved by [theNewDynamic](https://github.com/theNewDynamic).