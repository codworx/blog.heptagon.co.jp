# blog.heptagon.co.jp

## ブログ更新

```
git pull
bundle exec rake new_post[title]
vim source/_posts/2014-09-27-title.markdown
bundle exec rake generate
bundle exec rake preview
bundle exec rake deploy
git add .
git commit -m "message"
git push
```

## セットアップ

```
git clone git@github.com:youyo/blog.heptagon.co.jp.git
bundle install --path=vendor/bundle
cat <<EOL> ~/.aws/blog-config
AWS_ACCESS_KEY_ID='access_key'
AWS_SECRET_ACCESS_KEY='secret_access_key'
EOL
```
