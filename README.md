# Blog

# Structure

# Liquid Templating Language
-   ```
    {{ page.lang | default: site.lang | default: "en" }}
    ```
-   ```
    {% if site.paginate %}
        {% assign posts = paginator.posts %}
    {% else %}
        {% assign posts = site.posts %}
    {% endif %}
    ```

# Gem
- Gemfile : The Gemfile is a file used by Bundler to manage the dependencies for a Ruby project. It specifies the gems required for the project and their versions. When you run bundle install, Bundler reads the Gemfile to determine which gems to install and then records the exact versions in the Gemfile.lock file
- Gemfile.lock : automatically generated by Bundler, a dependency management tool for Ruby. It records the exact versions of the gems (Ruby libraries) that were installed when you last ran bundle install. This ensures that the same versions of the gems are used each time the project is set up, providing consistency across different environments and preventing potential issues caused by gem version changes. The Gemfile.lock file is crucial for maintaining a stable and reproducible environment for your Ruby project. It is typically committed to version control to ensure that all contributors and deployment environments use the same gem versions.



# Run locally
```
kill -9 $(lsof -t -i :4000)
bundle exec jekyll serve --config _config.local.yml --port 4001
```

# Cleanup
```
# this will break the locally running site
rm -rf .jekyll-cache _site
```