## It's Heroku Build Time (buildpack)

## What

Have you ever wanted to know if your code is running in build or runtime? Wonder no more, the `its_heroku_build_time_buildpack` is here to the rescue.

## Add it to your Heroku app

```
$ heroku buildpacks:add --index=1 https://github.com/schneems/its_heroku_build_time_buildpack
```

## In your Application Code

This buildpack sets an environment variable that you can detect only at build time. For a ruby application it can be checked like this:

```ruby
if ENV["ITS_HEROKU_BUILD_TIME"]
  puts "Heroku build time detected, calling specialized codepath"
  # Call code you only want to happen at build time on Heroku
  #
  # It won't get called locally, on CI, or at Heroku runtime
else
  puts "Detected that we are not running in a Heroku build."
  # Call code you want to happen every time EXCEPT for build time here.
end
```

## Warnings and fine print

It's generally not a good idea to add logic that diverges based on the platform or environment it's running on. For example, you can see the strong recommendation to use `RAILS_ENV=production` even when deploying to a staging app https://devcenter.heroku.com/articles/deploying-to-a-custom-rails-environment. That is because as configuration diverges it becomes harder to guarantee consistency. A core part of Heroku philosophy comes from the 12-factor app's "Dev/Prod parity" https://12factor.net/dev-prod-parity. By introducing a custom code path to production, you've now got a Build/Runtime disparity that might be confusing or lead to difficult to debug problems.

To aid, you should add some warnigns or print output to every codepath that can be affected by this environment variable, ideally both branches. The above output gives one example. This is a seemingly trivial, but crucial step as it might be the difference between a 10 minute fix and a 10 day deep dive to figure out why build time is behaving in unexpected ways that aren't reproducible.
