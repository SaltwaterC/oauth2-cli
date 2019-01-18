# Jeweler stuff
begin
  require 'jeweler'
  Jeweler::Tasks.new do |gem|
    gem.name        = 'oauth2-cli'
    gem.version     = '0.2.0'
    gem.summary     = %(CLI utility to get OAuth token)
    gem.description = %(CLI utility to get OAuth token for three-leggged flows)
    gem.author      = 'Ștefan Rusu'
    gem.email       = 'saltwaterc@gmail.com'
    gem.files       = %w[bin/oauth2-cli]
    gem.executables = %w[oauth2-cli]
    gem.license     = 'MIT'
    gem.homepage    = 'https://github.com/SaltwaterC/oauth2-cli'
  end
rescue LoadError
  STDERR.puts 'Jeweler, or one of its dependencies, is not available.'
end

begin
  # Rubocop stuff
  require 'rubocop/rake_task'
  RuboCop::RakeTask.new
rescue LoadError
  STDERR.puts 'Rubocop, or one of its dependencies, is not available.'
end

task default: [:rubocop]
