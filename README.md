# metadata-mod-hugo-dfd

DFD Hugo Module for Handling Metadata for Pages

## Status

![build-and-verify](https://github.com/danielfdickinson/metadata-mod-hugo-dfd/actions/workflows/build-and-verify.yml/badge.svg)

## Features

TBD

## Basic Usage

### Importing the Module

1. The first step to making use of this module is to add it to your site or theme.  In your configuration file:

   ``config.toml``
   ```toml
   [module]
     [[module.imports]]
       path = "github.com/danielfdickinson/metadata-mod-hugo-dfd"
   ```
   OR
   ``config.yaml``
   ```yaml
   module:
     imports:
       - path: github.com/danielfdickinson/metadata-mod-hugo-dfd
   ```
2. Execute
   ```bash
   hugo mod get github.com/danielfdickinson/metadata-mod-hugo-dfd
   hugo mod tidy
   ```
