# jupyter-blog-cookie-cutter
Turn a bunch of Jupyter notebooks into a website using [Nikola](https://getnikola.com), a static site builder. Below are the instructions

## Clone this repo
First fork this repo. Then, from a directory of your choice, run

```
git clone https://github.com/<your-user-name>/jupyter-blog-cookie-cutter.git
cd jupyter-blog-cookie-cutter
```

## Environment setup
You can use either `conda` or `virtualenv`. I prefer conda and have provided an `environment-osx.yml` file with this repo (in the `src` branch). Install this environment using the command:

```
conda env create -f environment-osx.yml
conda activate nikola
```
## Check if Nikola works
From the active nikola env, run

```
nikola
```

which should print something like below:

```
Nikola is a tool to create static websites and blogs. For full documentation and more information, please visit https://getnikola.com/


Available commands:
  nikola auto                 builds and serves a site; automatically detects site changes, rebuilds, and optionally refreshes a browser
  nikola build                run tasks
  nikola check                check links and files in the generated site
  nikola clean                clean action / remove targets
...
```
## Architecture of the website
This repo has `2` branches - `src` which contains the files you author and `gh-deploy` which contains the built HTML and related files that the web server needs. Nikola takes care of managing the `gh-deploy` branch and you need to seldom bother with it.

Broadly speaking, Nikola can build 2 types of sites - a **blog** type site which centers around a series of blogrolls. This is the most popular. The other type of site is called **pages** which resembles a typical website. This repo produces the latter (website, not a blog) as a website is better suited for most data science projects built using Jupyter notebooks. To learn more about building a [website with Nikola see here](https://getnikola.com/creating-a-site-not-a-blog-with-nikola.html).

Normally to create a project with Nikola, you need to run `nikola init` and answer a series of questions via command line. Upon completion, nikola would create a folder, populate with boilerplate files that you need to customize and populate with content. Since this repo is a *cookie cutter*, I have included the result of `nikola init` and other customization.

### What customization?
Here is a list, also these are the things you need to change based on your project. All configurations are on `conf.py` file:
 
 - Blog author, title, site_url, email, description. You need to update lines `19-28`.
 - Navigation links. Notice the **Project A/B/C** menu at the top? You specify those in lines `139` and onwards. You can also use nested links as mentioned in the comments section right above that line.
 - **Theme**. I use `bootstrap3`, which I installed using `nikola theme -i boostrap3` and is included in the `themes` folder. If you choose to use a different theme, you can pick from some of the [built in themes](https://themes.getnikola.com) or [customize or build a new theme](https://getnikola.com/creating-a-theme.html). Remember, you can remove unnecessary themes using `nikola theme -r <themename>` command.
 - I added `ipynb` as a file type to the `PAGES` tuple in line `221` and to `COMPILERS` in line `289`. This tells the compiler to convert notebook files to HTML. Nikola uses `nbconvert` lib with `basic.tpl` template to export the HTML. Later during `build` process, it adds the necessary css and html for the theme you choose.
 - `TIMEZONE` is set to Pacific (line `241`). Not important if you are not going to be posting blogs.
 - **GitHub publishing**: You can publish these html files via any web server, be it your company server or free (or paid) hosting sites such as Github pages. In this template, I chose Github pages and this is reflected in lines `636 - 644`. I chose to publish project pages instead of user pages, hence the `GITHUB_DEPLOY_BRANCH` is `gh-pages` instead of `master`. If you remember from earlier, the source files are in `src` branch while the built html files are in `gh-deploy` branch. The `GITHUB_COMMIT_SOURCE = True` flag tells the **deploy** command to auto commit appropriate source and built artifacts to both these branches. This saves you time and minimizes errors.      
  
   - **Note**: If you forked this repo, then you need to edit the `GITHUB_REMOTE_NAME` appropriately.


 - **Equations and scientific formulae**: To represent your equations correctly, I have enabled `MATHJAX` in lines `1028-1042`.
 - Each folder has a `index.md` file which summarizes that project and hyperlinks to sub pages. The metadata for these files needs `markdown.extensions.meta` enabled, which is done in line `1073`. To learn more [see here](https://getnikola.com/handbook.html#pelican-markdown-metadata).
 - **Site search**: You can make search engines such as Google, DuckDuckGo to index your site and provide site specific search. Here, I opted for the latter search engine. This is enabled in lines `1153-1166`.

## Editing, building, deploying
Now that you have customized the `conf.py` file, throw in all your notebook files into the `pages` folder or sub-folders within it. Then edit the `index.md` within each folder appropriately. You can include a summary of what each folder (project in this example) entails and a **hyperlink to each of the pages (notebooks) within it**. Next, 

 - run `nikola build` which will export the notebooks, markdown files into HTML pages into the `output` folder.
 - run `nikola github_deploy -m "your commit message"` which will smartly separate out the source files (Jupyter notebooks, markdown files, etc) from the output files (the build HTML, index files). It commits the source into the branch you picked for `GITHUB_SOURCE_BRANCH` variable and the output files into the branch you picked for the `GITHUB_DEPLOY_BRANCH` (which typically is `gh-pages`, unless you are deploying to GitHub user pages, in which case, it would be `master`). It uses the commit message you passed as the argument.

That's it. Your pages are built and pushed to remote. You can refresh your website and see the changes live.

From now on, to update your website, all you need to do, is edit / add notebook files, then simply run `github_deploy` command which build, commit, push and deploy for you.

### Notes:
  - `github_deploy` command will auto build the source. So you need not run the build command explicitly.
  - Running the build command helps you preview the changes before you make it final and commit. You can follow up the `build` command with the `serve` command to view it in your browser.
  - The `.gitignore` file will ignore the contents of the `output` folder which contains your built artifacts. This will however be included into the `gh-pages` branch, but in an exploded fashion.
  - `github_deploy` will commit and push to remote.

Happy blogging! The world needs more of good writers.