# Navigating Shopify

## Overview

In this codelab we are going to learn how to navigate the Shopify codebase by adding a working controller and view to the admin in your development environment.

## Prerequisites

**Basic knowledge**

1. Basic understanding of Rails
2. Basic understanding of Ruby
3. Basic understanding of git and github, see [Making your first PR](https://github.com/Shopify/codelabs/blob/master/making_pull_requests/README.md)
4. Have your Shopify development environment set up, see [Making your first PR](https://github.com/Shopify/codelabs/blob/master/making_pull_requests/README.md)

**Installed on your machine:**

1. Web browser
2. Code editor, see [recommended tools](https://vault.shopify.com/Shopify-Core-Development-Tools)
3. Shopify codebase

## Step 0: Overview of the code

Before we dive in let's take a look around the code base and get our bearings first.

1. Navigate and open up the Shopify project in your editor.  Ensure that you have run `dev up`.
2. If you look at the root folder you should see a list of folders that looks like this:
``` text
app/
bin/
borg/
buildkite/
ci/
config/
db/
eagerlib/
lib/
log/
node_modules
private/
public/
script/
test/
tmp/
vendor/

```
3. Drilling into the app directory we see more of Shopify itself:
``` text
app/
      assets/
      controllers/
          admin/
          api/
          concerns/
          customers/
          services/
          styleguide/
      experiments/
      exporters/
      helpers/
      jobs/
      liquid/
      mailers/
      middleware/
      models/
      presenters/
      serializers/
      services/
      tasks/
      validators/
      views/
          admin/
          carts/
          checkouts/
          services/
          ...
      wolverine/
bin/
borg/
buildkite/
ci/
config/
      routes.rb
db/

```

**Explanation:**
- app/models: This is where all domain classes live. They are usually `ActiveRecord::Base` subclasses (classes that interact with the databases).
- app/controllers: This is where the controller classes live. These handle loading and presenting the data for a request. Controllers are further organized by area such as admin, customers, services etc.
- app/views: These are the html (or html.erb to be more specific) views for the controller actions. These are organized in a similar manner as the controllers.
- config/routes.rb: This is the file that is responsible for linking the incoming request to the controller that will serve it. See the  [tracing a shopify request codelab](https://github.com/Shopify/codelabs/blob/master/trace_shopify_request/trace_shopify_request.md) for more details on request routing.

There are a lot more files and directories within the project, but these give you an overview of some of the core aspects to a Rails application.

## Step 1: Add a controller

Next we are going to start adding our controller to Shopify.

1. Create a file named `magic_controller.rb` in `app/controllers/admin/`.
2. Within that file paste the following code:
``` ruby
class Admin::MagicController < AdminAreaController
  layout 'admin/admin'

  def index
  end
end
```

**Explanation:**
- We are adding our controller within the admin directory since we want it accessible from the admin.
- We namespace the controller name with `Admin::` and the class name matches the file name in [CamelCase](http://en.wikipedia.org/wiki/CamelCase).
- The controller subclasses `AdminAreaController` so that we get admin behaviors baked in such as authentication, access control, error handling etc.
- The `layout` call specifies which [layout](http://guides.rubyonrails.org/layouts_and_rendering.html) the actions should be rendered within.

## Step 2: Add the route

1. Open the `config/routes.rb` file. This is where we specify which controllers requests are routed to.
2. Near the top of this file we are loading `config/routes/admin.rb`. The route file has been broken up and the admin routes are there. Open that file.
3. When adding a new route you should try and group it with anything related. In this case we will add it to the top for simplicity sake. Modify the admin.rb routes file to add the resource for our magic controller:
``` ruby
Rails.application.routes.draw do
  routing_method :shop_from_host do
    namespace :admin, defaults: { protocol: 'https://' } do
      [404, 403, 422, 500].each do |code|
        get code.to_s => "errors#error_#{ code }"
        resources :magic # add this line
        # ...
      end
      # ...
end
```
5. Start your Rails server by opening the Shopify project in terminal and run the command `dev server`.
4. Open up your browser and navigate to [https://shop1.myshopify.io/admin/magic](https://shop1.myshopify.io/admin/magic) and log into the admin using the default account (user: dev@shopify.com password: password) or authenticate via  [https://shop1.myshopify.io/admin/staff](https://shop1.myshopify.io/admin/staff). This is what you see:
![](missing_template.png).

**Explanation:**
- When we loaded that page we can see that it is routing to the correct controller, but it doesn't have a view template file to render.
- In the routes we are adding our route to the `magic` resource within the `admin` namespace so that it shows up there.

## Step 3: Adding the view

1. Add the view template to be rendered `app/views/admin/magic/index.html.erb` and add some html to it:
```html
<p>Magic Page</p>
```
2. Reload your page at `https://shop1.myshopify.io/admin/magic` and you should see something like below:
![](magic_view.png)

**Explanation:**
- Rails operates by convention so it knows to look for a controllers views under a directory with the same name in the ``app/views`` directory, e.g. `app/views/admin/magic/`.
- By default, rails will render the view template with the same name as the controller action that is evaluated, ``index.html.erb`` here.

## Next Steps

We didn't touch on testing, but every feature within Shopify should have tests added. Controller tests should be added to verify the behavior of the controller. Once you make a real change you will want to walk through [creating your first pr](https://github.com/Shopify/codelabs/blob/master/making_pull_requests/README.md) to learn how to create a pull request and get your code reviewed.

## Conclusion

This should set you up with some of the basics for navigating the codebase and start understanding how to work on Shopify. I'd encourage you to explore more of the codebase and notice more conventions and patterns that are used.
