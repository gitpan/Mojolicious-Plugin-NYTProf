# NAME

Mojolicious::Plugin::NYTProf - Auto handling of Devel::NYTProf in your Mojolicious app

<div>

</div>

<a href='https://travis-ci.org/leejo/mojolicious-plugin-nytprof?branch=master'><img src='https://travis-ci.org/leejo/mojolicious-plugin-nytprof.svg?branch=master' alt='Build Status' /></a>
<a href='https://coveralls.io/r/leejo/mojolicious-plugin-nytprof?branch=master'><img src='https://coveralls.io/repos/leejo/mojolicious-plugin-nytprof/badge.png?branch=master' alt='Coverage Status' /></a>

# VERSION

0.10

# DESCRIPTION

This plugin enables [Mojolicious](https://metacpan.org/pod/Mojolicious) to automatically generate Devel::NYTProf
profiles and routes for your app, it has been inspired by
[Dancer::Plugin::NYTProf](https://metacpan.org/pod/Dancer::Plugin::NYTProf)

# SYNOPSIS

    use Mojolicious::Lite;

    plugin NYTProf => {
      nytprof => {
        ... # see CONFIGURATION
      },
    };

    app->start;

Or

    use Mojo::Base 'Mojolicious';

    ...

    sub startup {
      my $self = shift;

      ...

      my $mojo_config = $self->plugin('Config');
      $self->plugin(NYTProf => $mojo_config);
    }

Then run your app. Profiles generated can be seen by visting /nytprof and reports
will be generated on the fly why you click on a specific profile.

# METHODS

## register

Registers the plugin with your app - this will only do something if the nytprof
key exists in your config hash

    $self->register($app, \%config);

# HOOKS AND Devel::NYTProf

The plugin adds hooks to control the level of profiling, Devel::NYTProf profiling
is started using a before\_routes hook and the stopped with an after\_dispatch hook.

The consequence of this is that you should see profiling only for your routes and
rendering code and will not see most of the actual Mojolicious framework detail.

You can override the hooks used to control when the profiling runs, see the
CONFIGURATION section below.

# CONFIGURATION

Here's what you can control in myapp.conf:

    {
      # Devel::NYTProf will only be loaded, and profiling enabled, if the nytprof
      # key is present in your config file, so either remove it or comment it out
      # to completely disable profiling.
      nytprof => {

        # path to your nytprofhtml script (installed as part of Devel::NYTProf
        # distribution). the plugin will do its best to try to find this so this
        # is optional, just set if you have a none standard path
        nytprofhtml_path => '/path/to/nytprofhtml',

        # path to store Devel::NYTProf output profiles and generated html pages.
        # options, defaults to "/path/to/your/app/root/dir/nytprof"
        profiles_dir => '/path/to/nytprof/profiles/'

        # set this to true to allow the plugin to run when in production mode
        # the default value is 0 so you can deploy your app to prod without
        # having to make any changes to config/plugin register
        allow_production => 0,

        # Devel::NYTProf environment options, see the documentation at
        # https://metacpan.org/pod/Devel::NYTProf#NYTPROF-ENVIRONMENT-VARIABLE
        # for a complete list. N.B. you can't supply start or file as these
        # are used internally in the plugin so will be ignored if passed
        env => {
          trace => 1,
          log   => "/path/to/foo/",
          ....
        },

        # when to enable Devel::NYTProf profiling - the pre_hook will run
        # to enable_profile and the post_hook will run to disable_profile
        # and finish_profile. the values show here are the defaults so you
        # do not need to provide these options
        #
        # N.B. there is nothing stopping you reversing the order of the
        # hooks, which would cause the Mojolicious framework code to be
        # profiled, or providing hooks that are the same or even invalid. these
        # config options should probably be used with some care
        pre_hook  => 'before_routes',
        post_hook => 'after_dispatch',
      },
    }

# AUTHOR

Lee Johnson - `leejo@cpan.org`

# LICENSE

This library is free software; you can redistribute it and/or modify it under
the same terms as Perl itself. If you would like to contribute documentation
please raise an issue / pull request:

    https://github.com/leejo/mojolicious-plugin-nytprof
