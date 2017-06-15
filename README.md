# tech4goodbristol.github.io
Tech4Good Bristol website
## Adding a new blog post
* Follow the instructions [here](http://cssbristol.co.uk/tutorials/post-on-the-site/) and [here](https://michaelsoolee.com/jekyll-post-page/)
* When the pull request has been merged to master (approved), GitHub will build it automatically and it will be live!

## Adding a new page
* Add a new page in the root or inside a folder. Add the link to `_data/nav.yml`. The nav bar will include the new page when the project is rebuilt (the master branch is updated).

---
## Developer section
### Generated static HTML files
* Layout files reside in `_layouts`. The main site layout is the file `layout.html`. 
```html
{% include header.html %}

    <main>
            {{ content }}

    </main>

{% include footer.html %}
```

* This is like PHP includes. `header.html` and `footer.html` reside in `_includes`.
    - Content is where the different page contents are rendered.
* The other files are like Wordpress custom templates. They get put into `{{ content }}` of `default.html`
    - `page.html` is for a single page
    - `post.html` is for a single blog post
    - `shout-out.html` is for a single shout out
* The code in curly braces is logic for rendering the static files. It's like rendering the HTML files for the *whole site* from a bunch of PHP files that contain includes. The rendered files will be stored in `_site`. This is in `.gitignore` as GitHub pages renders the site on a push to master anyway. So this is mainly for the developer's local static website that they can view on http://localhost:4000 (Jekyll server port). 

## Collections
* Collections are for collections of things. This is like Wordpress custom post types. Each collection is declared in the `_config.yml` file and has a subfolder beginning with an underscore e.g. `_shout_outs` (N.B. collection names can't have hyphens hence the underscore). Think of it as database tables where the collection subfolder e.g. `_shout_outs` is the database table and each `.md` Markdown file within it is a row in the table.
* The files can contain metadata which can be accessed using variables and rendered to the page.
* Take a look at `shout-outs.html`. There is a for-loop going through all shout outs and rendering each one as an individual table. This is like a PHP for-loop going through results from a SQL query and printing them out.
* In the folder `shout-outs` I have individual HTML files rendering the shout outs from each meetup. E.g. `shout-outs/2017-03-23.html` contain the shout outs from 23 May 2017. The rendering logic filters them by date:

```
{% for shout_out in site.shout_outs %}
            {% capture date_str %}{{ shout_out.date | date: '%Y-%m-%d' }}{% endcapture %}
            {% if date_str == '2017-03-23' %}
```


### Building
* Only for the developers. New pages and posts can be added without building.
* Install Docker and make sure it is running.
* Run this command to open up a Jekyll environment in a Docker container.

```bash
docker run --rm --label=jekyll --volume=$HOME/Sites/tech4goodbristol.github.io:/srv/jekyll --env GEM_HOME=/srv/jekyll/vendor/bundle  -it -p 127.0.0.1:4000:4000 jekyll/jekyll sh
```

* Change `$HOME/Sites/tech4goodbristol.github.io` to the **absolute path** to where this repo is located. `$HOME` is a bash alias pointing to the current user's home directory e.g. `/Users/nicole`.
* When your bash prompt shows `jekyll/jekyll #` you're inside the Jekyll environment. Type `exit` to exit the container and return to your bash prompt.
* On first run, run `bundle install`. This will install the Ruby dependencies into `vendor/bundle`.
* To build the website, run `jekyll serve`. It will build the static site and put the static files into `_site`. This is like the `public_html` that `http://localhost:4000` will look for. The Jekyll server runs on port 4000.
* Output of `jekyll serve`:

```bash
Configuration file: /srv/jekyll/_config.yml
Configuration file: /srv/jekyll/_config.yml
            Source: /srv/jekyll
       Destination: /srv/jekyll/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 2.16 seconds.
 Auto-regeneration: enabled for '/srv/jekyll''
Configuration file: /srv/jekyll/_config.yml
    Server address: http://0.0.0.0:4000/
  Server running... press ctrl-c to stop.
```

* It will watch for changes in the files and rebuild the whole static site. The local version is available on `http://localhost:4000`. 
* **Exit Jekyll** by running the command `exit`. The bash prompt now shows my Mac is Jekyll Docker has exited. The address `http://localhost:4000` won't be accessible now as port 4000 was the Jekyll server. To view the local site again, run the `docker run...` command and run `jekyll serve` inside it.

```bash
/srv/jekyll # exit
Nicoles-MBP:jekyll nicole$ 
```

* Once happy with the build, add the files (N.B. `_site` is ignored as GitHub will build their own static site version after a commit is pushed to master), make a new commit and make a pull request to make the changes live on [http://tech4goodbristol.github.io](http://tech4goodbristol.github.io)!

## Shout outs CSV to .md files
* The `utils` folder contains a Python script to parse a CSV and generate individual `.md` files for each shout out in `_shout_outs/yyyy-mm-dd`.
* `cd utils` and `python import_csv.py <shout out CSV>`. It pulls out the date from the file name of the CSV.
* Then in `shout-outs`, make a new file named `yyyy-mm-dd.html` by copying an existing one and change the line `{% if date_str == '2017-03-23' %}` to the new date.
