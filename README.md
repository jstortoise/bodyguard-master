# Bodyguard

Bodyguard protects the context boundaries of your application. 💪

Version 2.0 was built from the ground-up to integrate nicely with Phoenix contexts. Authorization callbacks can be implemented directly in contexts, so permissions can be checked from controllers, views, sockets, tests, and even other contexts.

To promote reuse and DRY up repetitive configuration, authorization can be constructed and executed in a composable way with `Bodyguard.Action`.

Additionally, the `Bodyguard.Schema` behaviour provides a convention for limiting user-accessible data from within the context.

This is an all-new API, so refer to [the `1.x` branch](https://github.com/schrockwell/bodyguard/tree/1.x) (still maintained!) if you are using versions prior to 2.0.

* [Docs](https://hexdocs.pm/bodyguard/) ← for complete documentation
* [Hex](https://hex.pm/packages/bodyguard)
* [GitHub](https://github.com/schrockwell/bodyguard)

## Quick Example

Define authorization rules directly in the context module, in this case `MyApp.Blog`:

```elixir
defmodule MyApp.Blog do
  @behaviour Bodyguard.Policy

  # Implement this callback:
  def authorize(:update_post, user, %{post: post}) do
    # Return :ok to permit
    # Return {:error, reason} to deny
  end
end

# Authorize a controller action (note the keyword list is converted to a map for the callback):
with :ok <- Bodyguard.permit(MyApp.Blog, :update_post, user, post: post) do
  # ...
end
```


## Installation

  1. Add `bodyguard` to your list of dependencies in `mix.exs`.

```elixir
def deps do
  [{:bodyguard, "~> 2.0.0"}]
end
```

  2. Create an error view for handling `403 Forbidden`.

```elixir
defmodule MyApp.ErrorView do
  use MyApp.Web, :view

  def render("403.html", _assigns) do
    "Forbidden"
  end
end
```

  3. Wire up a [fallback controller](#controllers) to render this view on authorization failures.

  4. Add `@behaviour Bodyguard.Policy` to contexts that require authorization, and implement the `authorize/3` callback.

  5. (Optional) Add `@behaviour Bodyguard.Schema` on schemas available for user-scoping, and implement the `scope/3` callback.

  6. (Optional) Edit `web.ex` and add `import Bodyguard` to controllers, views, channels, and anywhere else to take advantage of the top-level API.
