require 'fileutils'

module Dotfiles
  def self.each(&block)
    [
      '.xcolors/jasonwryan.xcolors',
      '.xcolors/zenburn.xcolors',
      '.gitconfig',
      '.gitignore',
      '.htoprc',
      '.lscolors',
      '.Xdefaults',
      '.zprofile',
      '.zshrc',
      '.oh-my-zsh',
      '.screen',
      '.vim'
    ].each do |file|
      yield Dotfile.new(file)
    end
  end

  class Dotfile
    include FileUtils

    attr_reader :dotfile
    attr_accessor :source, :target

    def initialize(dotfile)
      @dotfile = dotfile
      @source  = File.join(pwd, dotfile)
      @target  = File.join(ENV['HOME'], dotfile)
    end

    def install!
      puts "--> installing #{dotfile} as #{target}..."
      if File.exists?(target) || File.symlink?(target)
        mv target, "#{target}.backup", :verbose => true
      end

      ln_s source, target, :verbose => true
    end
  end
end

desc "updates all submodules"
task :submodules do
  unless system('git submodule update --init')
    raise 'error initializing submodules'
  end
end

desc "installs all dotfiles into the proper places"
task :install => [:submodules] do
  Dotfiles.each(&:install!)

  Dir.chdir('.vim') do
    unless system('git submodule update --init')
      raise 'error initializing vim submodules'
    end
  end

  vimrc = Dotfiles::Dotfile.new('.vimrc')
  vimrc.source = File.join(ENV['HOME'], '.vim', 'vimrc')
  vimrc.install!
end

task :default => :install