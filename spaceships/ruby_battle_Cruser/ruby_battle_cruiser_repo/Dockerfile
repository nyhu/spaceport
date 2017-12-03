FROM ruby:2.4.1

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs sendmail sendmail-cf && rm -rf /var/lib/apt/lists/*

# App specific installations are run separatelly so previous is a rehused container
RUN apt-get install -y imagemagick && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY Gemfile Gemfile.lock ./

RUN gem install bundler && bundle install --jobs 20 --retry 5 --without development --without test

# Set Rails to run in production
ENV RAILS_ENV production
ENV RACK_ENV production

# If a db:migrate:reset is necessary, uncomment the following line.
# ENV DISABLE_DATABASE_ENVIRONMENT_CHECK 1

COPY . ./
EXPOSE 3000
EXPOSE 25

# Precompile Rails assets. We set a dummy secret key
RUN bundle exec rake RAILS_ENV=production assets:clean assets:precompile --trace

CMD ["sh", "run.sh"]
