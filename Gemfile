source "http://rubygems.org"

omnibus_ruby_local_path = File.expand_path(File.join(File.dirname(__FILE__), "..", "omnibus-ruby"))
omnibus_software_local_path = File.expand_path(File.join(File.dirname(__FILE__), "..", "omnibus-software"))

#gem "omnibus", :path => omnibus_ruby_local_path
gem "omnibus", :git => "http://github.com/jssjr/omnibus-ruby", :branch => "freebsd-support"

#gem "omnibus-software", :path => omnibus_software_local_path
gem "omnibus-software", :git => "http://github.com/jssjr/omnibus-software", :branch => "freebsd-support"

group :development do
  gem "vagrant", "~> 1.0"
end

