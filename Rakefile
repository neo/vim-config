require 'rake'
require './lib/installer'
require './lib/translation'

NIX_FILES = [
  [ 'nvim', '~/.config/nvim'],
  [ 'nvim', '~/.vim'],
  [ 'nvim/init.vim', '~/.vimrc'],
  [ '.gvimrc', '~/.gvimrc'],
]

desc "Install vim configuration and plugin files"
task :default do
  installer = Installer.new(platform_files)
  installer.files.each do |f|
    case
      when f.identical?    then skip_file(f)
      when replace_all?    then auto_link_files(f)
      when f.safe_to_link? then auto_link_files(f)
      else                      prompt_to_link_files(f)
    end
  end
  Rake::Task['nebundle'].execute
end

desc "Install neobundle for vim plugins"
task :nebundle do
  target = "#{platform_files[0][1]}/bundle/neobundle.vim"
  Installer.git_clone('https://github.com/Shougo/neobundle.vim', target)
  puts "Running BundleInstall to install plugins...this will take a couple minutes."
  `vim -e +NeoBundleInstall +qall`
  puts "vim plugins installed."
end

def platform_files
  NIX_FILES
end

def prompt_to_link_files(file)
  print "overwrite? #{file.target} [ynaq]  "
  case $stdin.gets.chomp
    when 'y' then replace(file)
    when 'a' then replace_all(file)
    when 'q' then exit
    else          skip_file(file)
  end
end

def link_files(file)
  puts " => symlinking #{file.source} to #{file.target}"
  file.link
end

def replace(file)
  puts " => replacing #{file.source} with #{file.target}"
  file.force_link
end

def replace_all(file)
  @replace_all = true
  replace(file)
end

def replace_all?
  @replace_all == true
end

def skip_file(file)
  puts " => skipping #{file.target}"
end

def auto_link_files(file)
  file.safe_to_link? ? link_files(file) : replace(file)
end
