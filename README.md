# EctoAutoslugField (slugify-fork)

[![Build Status](https://travis-ci.org/sobolevn/ecto_autoslug_field.svg?branch=master)](https://travis-ci.org/sobolevn/ecto_autoslug_field) [![Coverage Status](https://coveralls.io/repos/github/sobolevn/ecto_autoslug_field/badge.svg?branch=master)](https://coveralls.io/github/sobolevn/ecto_autoslug_field?branch=master) [![Hex Version](https://img.shields.io/hexpm/v/ecto_autoslug_field.svg)](https://hex.pm/packages/ecto_autoslug_field) [![License](http://img.shields.io/badge/license-MIT-brightgreen.svg)](http://opensource.org/licenses/MIT)

`ecto_autoslug_field` is a reusable [`Ecto`](https://github.com/elixir-ecto/ecto) library which can automatically create slugs from other fields.

This library internally uses [`slugify`](https://github.com/jayjun/slugify) as it's default slug-engine.

You can find the full documentation online: [docs](https://hexdocs.pm/ecto_autoslug_field).

## Installation

```elixir
def deps do
  # installation via hex (version 0.3 and onwards only supports `{:ecto, ">= 2.1"}`):
  [{:ecto_autoslug_field, "~> 0.3"}]

  # if you want to use github:
  # [{:ecto_autoslug_field, github: "sobolevn/ecto_autoslug_field"}]

  # if you need support for `{:ecto, "~> 2.0"}`:
  # [{:ecto_autoslug_field, "~> 0.2"}]

  # if you need support for older Ecto versions (< 2.0):
  # [{:ecto_autoslug_field, "~> 0.1.3"}]
end
```

## Options

There are several options to configure.

Required:

- `:to` - represents the slug field name where to `put_change` to

Optional:

- `:from` - represents the source fields from which to build slug, if this option is not set you have to override `get_sources/2` function
- `:always_change` - if this option is set slug will be recreated from the given sources each time `maybe_generate_slug` function is called

## Functions

- `get_sources/2` - this function is used to get sources for the slug, [docs](https://hexdocs.pm/ecto_autoslug_field/EctoAutoslugField.SlugBase.html#get_sources/2).
- `build_slug/2` - this function is a place to modify the result slug, [docs](https://hexdocs.pm/ecto_autoslug_field/EctoAutoslugField.SlugBase.html#build_slug/2).

## Examples

The simplest example:

```elixir
defmodule EctoSlugs.Blog.Article.TitleSlug do
  use EctoAutoslugField.Slug, from: :title, to: :slug
end

defmodule EctoSlugs.Blog.Article do
  use Ecto.Schema
  import Ecto.Changeset
  alias EctoSlugs.Blog.Article
  alias EctoSlugs.Blog.Article.TitleSlug

  schema "blog_articles" do
    field :breaking, :boolean, default: false
    field :content, :string
    field :title, :string

    field :slug, TitleSlug.Type

    timestamps()
  end

  def changeset(%Article{} = article, attrs) do
    article
    |> cast(attrs, [:title, :content, :breaking])
    |> validate_required([:title, :content])
    |> unique_constraint(:title)
    |> TitleSlug.maybe_generate_slug
    |> TitleSlug.unique_constraint
  end
end
```

More complex examples are covered in [this tutorial](https://medium.com/wemake-services/creating-slugs-for-ecto-schemas-7349513410f0).

## Changelog

See [CHANGELOG.md](https://github.com/sobolevn/ecto_autoslug_field/blob/master/CHANGELOG.md).

## License

MIT. Please see [LICENSE.md](https://github.com/sobolevn/ecto_autoslug_field/blob/master/LICENSE.md) for licensing details.
