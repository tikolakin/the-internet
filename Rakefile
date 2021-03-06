desc 'Generate test config file (used by Travis CI)'
task :generate_ci_test_config do
  config_file_text = File.read('tests/config.yaml.template')
  config_file_text.gsub!(/<%= sauce_username %>/, ENV['SAUCE_USERNAME'])
  config_file_text.gsub!(/<%= sauce_api_key %>/, ENV['SAUCE_ACCESS_KEY'])
  config_file_text.gsub!(/<%= build %>/, ENV['TRAVIS_BUILD_NUMBER'])
  config_file_text.gsub!(/<%= tunnel_identifier %>/, ENV['TRAVIS_JOB_NUMBER'])
  File.open('tests/config_ci.yaml', 'w') { |file| file.puts config_file_text }
end

desc 'Runs Acceptance Tests'
task :test do
  system('cd tests && sudo bundle exec ckit brew')
end

desc 'Update docs'
task :update_docs  do
  update_readme
  update_examples_view
end

desc 'Pushes tags and code to master, develop, and heroku'
task :release do
  `git push origin master ; git push origin develop ; git push --tags ; git push heroku master`
end

def examples(file = 'examples.csv')
  require 'csv'
  examples = CSV.read(file, headers: true, header_converters: :symbol, converters: :all)

  examples.collect do |row|
    Hash[row.collect { |key, value| [key, value] }]
  end.sort_by { |key| key[:title] }

  # Returns an Array of Hashes in alphabetical order by :title like so:
  # {:title=>"A/B Testing", :url_path=>"/abtest", :notes=>nil}
  # {:title=>"Basic Auth", :url_path=>"/basic_auth", :notes=>"user and pass: admin"}
end

def convert_to_markdown_links(examples)
  markdown_string = ""
  examples.each do |example|
    markdown_string << "+ [#{example[:title]}](http://the-internet.herokuapp.com#{example[:url_path]})"
    markdown_string << " (#{example[:notes]})" if example[:notes]
    markdown_string << "\n"
  end
  markdown_string
end

def update_readme
  readme_text = File.read('README.template')
  readme_text.gsub!(/<%= examples %>/, convert_to_markdown_links(examples))
  File.open('README.md', 'w') { |file| file.puts readme_text }
end

def update_examples_view
  index_html = "<ul>\n"
  examples.each do |example|
    index_html << "  <li><a href='#{example[:url_path]}'>#{example[:title]}</a>"
    index_html << " (#{example[:notes]})" if example[:notes]
    index_html << "</li>"
    index_html << "\n"
  end
  index_html << "</ul>"
  File.open('views/examples.erb', 'w') { |file| file.puts index_html }
end
