---
version: 4.3.1
---

# Installed R packages

The following list of packages is installed in R {{ version }}.

Last updated: _{{ git_revision_date_localized }}_

<!-- 
// Copyright 2014-2022 Stanford Research Computing Center
//
// The following code is a derivative work of https://raw.githubusercontent.com/stanford-rc/www.sherlock.stanford.edu/main/src/docs/software/list.md, which is licensed GPLv3. This code therefore is also licensed under the terms GPLv3.
-->

{% macro package_line(p) -%}
    {{- p.Package }} | {{ p.Version }} | {{ p.Title }}
{%- endmacro %}

Package | Version | Description
:------ | :------ | :----------
{% for p in packages | sort(attribute="Package") -%}
  {{ package_line(p) }}
{% endfor -%}