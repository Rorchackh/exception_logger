# WARNING
Please do not use this project. It is customized for my personal use. Please use the original project from which this is forked.

# Exception Logger for Rails 3.2

This gem is evolved from the outdated exception_logger gem. I refined it with the mountable rails engine. That make it conveniet to
mount it to any existing rails app.

## Install

Inside your Gemfile:

  gem "exception_logger", :github => '<use this repo>', :branch => 'rails-3.2'

and then run:

  bundle install

### Gem Dependencies

Please check if all those requirements are satisfied on your environment.

* will_paginate > 3.0.3
* rails >= 3.2 and rails < 4.0

### Inside your ApplicationController:

  include ExceptionLogger::ExceptionLoggable # loades the module
  rescue_from Exception, :with => :log_exception_handler # tells rails to forward the 'Exception' (you can change the type) to the handler of the module

### Inside your config/routes.rb

  mount ExceptionLogger::Engine => "/exception_logger"

### Database Setup

Use

    rake exception_logger:install:migrations

This will create a database migration

    TIMESTAMP_create_exception_logger_logged_exceptions.exception_logger.rb

inside db/migrations

Then

    rake db:migrate

## Authentication

If you want, and believe me you want that, you can add the following lines to your environment to enable Authentication 

Example uses the declarative_authorization gem

```
in config/environment/production.rb:

  config.after_initialize do
    LoggedExceptionsController.class_eval do
      # include any custom auth modules you need
      # (declarative authorization gem used)
      filter_resource_access
      # just add any code you want to be included in the controller ;)
      # optional, sets the application name for the rss feeds
      self.application_name = "Your Application Name"
    end
  end
```

and (if you use declarative_authorization) add this to your authorization_rules.rb file:

```
  role :admin do
    has_permission_on :logged_exceptions, :to => :manage
    ...
```

## Testing & Development

Not implemented yet.

## Usage

To localize the interface:

```
  en:
    exception_logger:
      logged_exceptions:
        index:
          title: Logged Exceptions
          filters: Filters
          latest_exceptions: Latest Exceptions
          exception: Exception
          controller_action: Controller / Action
          dates: Dates
          today: Today
          last_few_days: Last few days
          last_7_days: Last 7 days
          last_30_days: Last 30 days
        show:
          title: Logged Exception
```

To log extra data, in your ApplicationController:

    self.exception_data = Proc.new { |controller| "Extra exception infomation here!" }

or

```
  self.exception_data = :extra_exception_data

  def extra_exception_data
    "Extra exception infomation here!"
  end
```

To add authentication, in config/application.rb or the specific environment file:

```
  # set up authencation of exception_logger
  config.after_initialize do
    ExceptionLogger::LoggedExceptionsController.class_eval do
      before_filter :authorize

      protected

      def authorize
        redirect_to main_app.admin_login_path unless admin_loggined?
      end

      def admin_loggined?
        session[:admin_user_id].present?
      end
    end
  end
  
```

##License

MIT License. See LICENSE for details.

##Copyright

Copyright (c) 2012 Ryan Cheung and Shanghai Boohee Information Technology Co. Ltd.
