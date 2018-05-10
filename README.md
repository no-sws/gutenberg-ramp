# Gutenberg Ramp

### Overview

Gutenberg Ramp is a plugin that manages the state of Gutenberg in the post-edit context.  It loads or unloads Gutenberg in post-edit according to criteria specified in theme code.  It is agnostic about whether Gutenberg is loading from core or via the plugin.

### How it Works

Gutenberg Ramp assumes one of the following states:

- WordPress 4.9 and the Gutenberg plugin (either activated or not)
- WordPress 5.0 and the Classic Editor plugin 

If it detects neither of these conditions, it will do nothing.

Gutenberg Ramp makes a decision early in the WordPress load sequence (`plugins_loaded`) about whether to take action.  It will take action if the following are true:

- the `wp-admin/post.php` is going to load AND
- according to code-configured criteria either: Gutenberg should load for the current post and will not OR Gutenberg shouldn't load for the current post and will.

The currently supported criteria are post ID (load for only specified posts) and post type (load only for specified post types).  You can also instruct Gutenberg to never or always load.

### Theme code

Criteria are stored in an option and specified by calling a function any time after `plugins_loaded`, typically in theme code or on a hook such as `init`.

Loading behavior is controlled by the `ramp_for_gutenberg_load_gutenberg()` function.  Calling this function without its single optional parameter causes Gutenberg to load on all post-edit screens.  An optional associative array of criteria can be passed.  The possible keys and values are:

- `load` (Int): `0|1`:  never or always load Gutenberg
- `posts` (Array of post_ids): loads Gutenberg for the specified post_ids
-  `post_types` (Array of post_types): loads Gutenberg for the specified post types.

### Examples

```
if ( function_exists( 'ramp_for_gutenberg_load_gutenberg' ) ) {
	ramp_for_gutenberg_load_gutenberg();
}
```

Load Gutenberg for all posts.

`ramp_for_gutenberg_load_gutenberg( [ 'load' => 0 ] );`

Do not load Gutenberg for any posts.

`ramp_for_gutenberg_load_gutenberg( [ 'post_ids' => [ 12, 13, 122 ] ] );`

Load Gutenberg for posts with ids 12, 13 and 122.

`ramp_for_gutenberg_load_gutenberg( [ 'post_types' => [ 'test', 'scratch' ], 'post_ids' => [ 12 ] ] );`

Load Gutenberg for post_id 12 and all posts of type `test` and `scratch`

### Advanced	

The typical use case is as shown above, the parameters do not change except when theme code is updated.	

If making more dynamic changes, note that the parameter supplied is persisted in a site option; when the parameters are changed in code, one page load is necessary to update the site option before the editor can use the new setting.

### FAQs

**Why is a post type disabled (greyed out) at Settings > Writing?**

If you're seeing something greyed out, it means the `ramp_for_gutenberg_load_gutenberg()` function is already in your theme functions.php. If you want to use the wp-admin UI, remove the conflicting function from your functions.php file. 

**Some post types are not showing up on the settings screen**

Post types that are not compatible with Gutenberg will not show up. If you think you have found a false negative (posts in that post type DO work with Gutenberg, when Ramp plugin is deactivated) please report it as an issue on [GitHub here.](https://github.com/Automattic/ramp-for-gutenberg)

**The changes I'm making in functions.php are not showing up**

The parameter supplied in the function is persisted in a site option. Therefore, when the parameters are changed in code, _one page load is necessary_ to update the site option before the editor can use the new setting.
