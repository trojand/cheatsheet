# cheatsheet

To build this for offline use (Yes it's a give away for what 3rd party dependency to compromise)
```
sudo apt install git
pip3 install mkdocs mkdocs-material mkdocs-material-extensions pymdown-extensions mkdocs-git-revision-date-localized-plugin mkdocs-git-revision-date-plugin
git clone https://github.com/trojand/cheatsheet/
cd cheatsheet
mkdocs build --no-directory-urls
```

Run
```
firefox site/index.html
# or
mkdocs serve
firefox http://127.0.0.1:8000/cheatsheet/
```
