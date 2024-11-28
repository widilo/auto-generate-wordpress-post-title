# Auto generate wordpress post title from first line

### Overview:

The `auto_generate_title_from_first_line` function automatically generates a post title based on the **first line** of the post content when a post is saved or updated. The function works by extracting the text before the first line break (`\n`), removing any HTML tags, and trimming spaces. It then sets the post title to this text, but only if the title is not already set.

------

### Function Signature:

```php
function auto_generate_title_from_first_line($post_id);
```

------

### Parameters:

- **`$post_id` (int)**: The ID of the post being saved or updated. WordPress automatically passes this parameter when the `save_post` action is triggered.

------

### Logic:

1. **Check Post Type and Autosave:**
   - The function first checks if the post is of type `post` (i.e., not a page or other custom post type).
   - It also ensures that the function does not run during WordPress's autosave process by checking the `DOING_AUTOSAVE` constant.
2. **Get Post Content:**
   - The function retrieves the post content using `get_post_field('post_content', $post_id)`.
3. **Extract First Line:**
   - The function extracts the **first line** of the content by splitting the content at the first line break (new line) using `strtok($post_content, "\n")`. This ensures that only the content before the first line break is selected.
4. **Check for Empty Line:**
   - The function checks if the extracted first line is empty. If it is, the function stops and does not update the post title.
5. **Remove HTML Tags and Trim Whitespace:**
   - The first line is passed through `wp_strip_all_tags()` to remove any HTML tags that may be present.
   - The function then trims leading and trailing spaces using `trim()` to ensure the title is clean.
6. **Check and Set Post Title:**
   - The function retrieves the current post title using `get_post_field('post_title', $post_id)`.
   - If the post title is empty (i.e., not set), it updates the post title to the cleaned first line using `wp_update_post()`.

------

### WordPress Hook:

This function is hooked into the `save_post` action, which means it runs every time a post is saved or updated.

```php
add_action('save_post', 'auto_generate_title_from_first_line');
```

------

### Use Case:

This function is ideal for automatically generating a title from the post content when the title is not manually provided. It ensures that a relevant, content-based title is set when a new post is created or updated.

------

### Example:

Consider the following post content:

```
This is the first line of the post content.
This is the second line of the content.
```

- The function extracts "This is the first line of the post content." as the first line.
- If the post title is empty, the function sets the post title to "This is the first line of the post content."

------

### Notes:

- The function **only** sets the title if the current title is empty. If the post already has a title, the title remains unchanged.
- HTML tags are stripped from the first line before it is set as the title. This ensures the title is clean and without any HTML markup.
- The function uses the first line of the post content, so it does not consider the full paragraph or other content.

------

### Error Handling:

- If the first line is empty, no changes are made, and the title is not updated.
- If the post content does not have a first line (i.e., when it's empty), the function does nothing.

------

### Here’s the full function code:

```php
function auto_generate_title_from_first_line($post_id) {
    // Only for posts (post type) and not during the auto-saving process
    if (get_post_type($post_id) != 'post' || defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) {
        return;
    }

    // Get the content of the post
    $post_content = get_post_field('post_content', $post_id);
    
    // Extract the first line by splitting at the first line break (new line)
    $first_line = strtok($post_content, "\n");

    // Check if the first line is empty
    if (empty($first_line)) {
        return;
    }

    // Remove HTML tags (if any) and trim any leading or trailing spaces
    $first_line = wp_strip_all_tags(trim($first_line));

    // Only change the title if it is not set or if it is empty
    $post_title = get_post_field('post_title', $post_id);
    if (empty($post_title)) {
        // Set the title to the first line of the content
        wp_update_post(array(
            'ID' => $post_id,
            'post_title' => $first_line
        ));
    }
}
add_action('save_post', 'auto_generate_title_from_first_line');
```

------

### Conclusion

The `auto_generate_title_from_first_line` function is a simple and effective way to automatically generate post titles based on the content. By using the first line of the content, this function ensures that titles are relevant to the body of the post, while also ensuring that HTML tags are stripped out and extra spaces are removed.

### Changelog
Version 1.0

    Initial release: Automatically generates titles based on the first line of the content.

### Support

If you encounter any issues with the code or have suggestions for improvement, please contact the developer: hallo@widilo.de

### License

This Code is released under the GNU General Public License (GPL v.3)
