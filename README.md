# How to

## Create a post

```sh
hexo new post post-title
```

## Preview the site

```sh
npm run server
```

## Publish

```sh
git add .
git commit -m "feat: some message"
git push
```

## CI/CD

Base on github action, when push on branch main, it will build the source folder and generate the publish files to gh-pages.