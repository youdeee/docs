# 新しくrailsプロジェクト立ち上げるときの手順
- mkdir
- bundle init
- bundle
- bundle exec rails new . -d postgresql --skip-turbolinks -T
- editorconfig
- rubocop
  - gem "rubocop-rails_config", require: false
  - gem 'rubocop-performance', require: false
- rspec
  - factory_bot_rails capybara webdrivers
  - rails g rspec:install
  - .rspec helperいじる
  - config/applicationのgeneratorsの設定いじる
- pry pry-rails pry-byebug
- config.time_zone = "Tokyo"
- locale
    config.i18n.available_locales = %i(ja en)
    config.i18n.default_locale = :ja
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}').to_s]
## devise使った認証ユーザー使う場合
### home
  - rails g controller homes show
### devise
  - gem devise-i18n letter_opener_web
  - rails g devise:install
  - routes mount LetterOpenerWeb::Engine, at: "/letter_opener" if Rails.env.development?
  - config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
  - config.action_mailer.delivery_method = :letter_opener_web
  - rails g devise user
  - ~ableいじる model/migration
  - rails db:migrate
  - rails g devise:i18n:views user
### deviseのパスのカスタマイズ
  - rails generate devise:controllers users
  - rails g devise:i18n:locale ja deviseをusersに
```
  devise_scope :user do
    get "signup", to: "users/registrations#new", as: :new_user_registration
  end
  devise_for :users,
             skip: %i(sessions),
             controllers: {
               registrations: "users/registrations",
               passwords: "users/passwords"
             }
  as :user do
    get "login", to: "users/sessions#new", as: :new_user_session
    post "logout", to: "users/sessions#create", as: :user_session
    match "logout", to: "users/sessions#destroy", as: :destroy_user_session, via: Devise.mappings[:user].sign_out_via
  end
```
