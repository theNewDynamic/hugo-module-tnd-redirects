# TND Redirects Hugo Module

This module helps create and maintain redirections for your Hugo project using Hugo's aliases params or a set of custom redirect rules added either via configutation or a dynamic returning partial on the project's level.

It supports beautiful Netlify but should scope out to more services in the future.

## What are redirections?

We call redirection anything that takes an initial URL to match (the _origin_), and redirects to another destination, (the _target_).
The module processes a list of rules, defined by the user through either the project's configuration file or a returning partials and make sure their correctly processed by a given hosting service.

## Quick setup

## 1. Add module to your project's imports:

```yaml
# config.yaml
module:
  imports:
    - path: github.com/theNewDynamic/hugo-module-tnd-redirects
```

### 2. Add redirect output format for chosen service.

#### Netlify

Set configuration so Hugo produces the redirections file at the root of the site (on the Homepage)

```yaml
# config.yaml

outputs:
  homepage: 
    - HTML
    - tnd_hredirects_netlify
    # + any other outputs needed on the homepage.
```
OR

```yaml
# content/_index.md
title: Homepage
homepage: 
  - HTML
  - tnd_redirects_netlify
  # + any other outputs needed on the homepage.
```

## API

Any given rule properties are defined by the following keys. Those marked by an * are mandatory

- __origin__\*: the URL to match
- __target__\*: the destination
- __code__: the status code (default: 301)
- __force__: Is it a force redirect? (default: false)
- __country__: the country if redirection is subject to a country code
- __language__: the language if redirection is subject to a language code
- __role__: the user role if redirection is subject to a user role

## Configuration

Configuration is set through the site's configurationf file's using the `tnd_redirects` reserved keys.

### use_aliases 

Should the module build redirect rules for entries using Hugo's reserved `aliases` Front Matter key. 
If so, you should disable Hugo's own Aliases feature with `disableAliases: true`

```yaml
# config.yaml

disableAliases: true

params:
  tnd_redirects:
    use_aliases: true
```




### redirects

A list of custom redirect rules. See below.

## Adding Custom Redirects

### Though Configuration File (hardcoded)
User can add a list of redirects through the site's configuration file. Each rule can use the API's available keys.

```yaml
# config.yaml
params:
  tnd_redirects:
    rules:
      - origin: /something
        target: /something-else
      - origin: /contact
        target: /fr/contact
        language: fr
```

### Through a Returning Partial (dynamnic)

In order to add dynamic redirect rules built by Hugo, user can create a dedicated returning partial in the project at `layouts/partials/tnd-redirects/AddRules.html`. 
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
  {{/* We create a map for a rule with origin, target and country key*/}}
  {{ $rule := dict "origin" .RelPermalink "target" "/canadian-regulation" "country" "ca" }}

  {{/* We add the rule's map to the $redirs slice */}}
  {{ $redirs = $redirs | append $rule }}
{{ end }}

{{ return $redirs }}
```

## theNewDynamic

This module is created, maintained and loved by [theNewDynamic](https://github.com/theNewDynamic).
