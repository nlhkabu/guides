Sometimes you want to redirect a user to a different page than what they requested for.

For example, if they're not logged in, you might want to prevent them from editing their profile, accessing private information, or checking out items in their shopping cart. Usually you want to redirect them to the login page, and after they have successfully logged in, take them back to the page they originally wanted to access.

There are many other reasons you probably want to have the last word on whether a user can or cannot access a certain page. Ember allows you to control that access with a combination of hooks and methods in your route.

One of the methods is [`transitionTo()`](http://emberjs.com/api/classes/Ember.Route.html#method_transitionTo). Calling `transitionTo()` from a route or [`transitionToRoute()`](http://emberjs.com/api/classes/Ember.Controller.html#method_transitionToRoute) from a controller will stop any transitions currently in progress and start a new one, functioning as a redirect. `transitionTo()` behaves exactly like the [link-to](../../templates/links) helper.

The other one is [`replaceWith()`](http://emberjs.com/api/classes/Ember.Route.html#method_replaceWith) which works the same way as `transitionTo()`. The only difference between them is how they manage history. `replaceWith()` substitutes the current route entry and replaces it with that of the route we are redirecting to, while `transitionTo()` leaves the entry for the current route and creates a new one for the redirection.

If the new route has dynamic segments, you need to pass either a *model* or an *identifier* for each segment. Passing a model will skip the route's `model()` hook since the model is already loaded.

## モデルがわかる前の、遷移

Since a route's [`beforeModel()`](http://emberjs.com/api/classes/Ember.Route.html#method_beforeModel) executes before the `model()` hook, it's a good place to do a redirect if you don't need any information that is contained in the model.

```app/router.js Router.map(function() { this.route('posts'); });

    <br />```app/routes/index.js
    import Ember from 'ember';
    
    export default Ember.Route.extend({
      beforeModel(/* transition */) {
        this.transitionTo('posts'); // Implicitly aborts the on-going transition.
      }
    });
    

`beforeModel()` receives the current transition as an argument, which we can store and retry later. This allows us to return the user back to the original route. For example, we might redirect a user to the login page when they try to edit their profile, and immediately redirect them back to the edit page once they have successfully logged in. See [Storing and Retrying a Transition](../preventing-and-retrying-transitions/#toc_storing-and-retrying-a-transition) for how to do that.

If you need to examine some application state to figure out where to redirect, you might use a [service](../../applications/services).

## モデルがわかる前の、遷移

If you need information about the current model in order to decide about redirection, you can use the [`afterModel()`](http://emberjs.com/api/classes/Ember.Route.html#method_afterModel) hook. It receives the resolved model as the first parameter and the transition as the second one. For example:

```app/router.js Router.map(function() { this.route('posts'); this.route('post', { path: '/post/:post_id' }); });

    <br />```app/routes/posts.js
    import Ember from 'ember';
    
    export default Ember.Route.extend({
      afterModel(model, transition) {
        if (model.get('length') === 1) {
          this.transitionTo('post', model.get('firstObject'));
        }
      }
    });
    

When transitioning to the `posts` route if it turns out that there is only one post, the current transition will be aborted in favor of redirecting to the `PostRoute` with the single post object being its model.

### チャイルドルート

Let's change the router above to use a nested route, like this:

```app/router.js Router.map(function() { this.route('posts', function() { this.route('post', { path: '/:post_id' }); }); });

    <br />`afterModel` フックで`posts.post` にリダイレクトしたとき`afterModel`は基本的にこのルートに入ることを無効化します。 そうすることで`posts`ルートは`beforeModel`、`model`そして `afterModel` フックが再度、新規のリダイレクトされた遷移で起動されます。 これはリダイレクトの直前で起こるため、効率的ではありません。
    
    Instead, we can use the [`redirect()`](http://emberjs.com/api/classes/Ember.Route.html#method_redirect) method, which will leave the original
    transition validated, and not cause the parent route's hooks to fire again:
    
    ```app/routes/posts.js
    import Ember from 'ember';
    
    export default Ember.Route.extend({
      redirect(model, transition) {
        if (model.get('length') === 1) {
          this.transitionTo('posts.post', model.get('firstObject'));
        }
      }
    });