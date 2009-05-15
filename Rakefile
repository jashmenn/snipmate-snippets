desc "Copy the snippets directories into ~/.vim/snippets"
task :deploy_local do
  run "rm -rf ~/.vim/snippets"
  run "mkdir ~/.vim/snippets"
  run "cp -r _ ~/.vim/snippets/"
  run "cp -r ant ~/.vim/snippets/"
  run "cp -r c ~/.vim/snippets/"
  run "cp -r eruby ~/.vim/snippets/"
  run "cp -r eruby-rails ~/.vim/snippets/"
  run "cp -r html ~/.vim/snippets/"
  run "cp -r java ~/.vim/snippets/"
  run "cp -r javascript ~/.vim/snippets/"
  run "cp -r javascript-jquery ~/.vim/snippets/"
  run "cp -r objc ~/.vim/snippets/"
  run "cp -r php ~/.vim/snippets/"
  run "cp -r python ~/.vim/snippets/"
  run "cp -r ruby ~/.vim/snippets/"
  run "cp -r ruby-factorygirl ~/.vim/snippets/"
  run "cp -r ruby-rails ~/.vim/snippets/"
  run "cp -r ruby-rails-rjs ~/.vim/snippets/"
  run "cp -r ruby-rspec ~/.vim/snippets/"
  run "cp -r vim ~/.vim/snippets/"
  run "cp -r zend ~/.vim/snippets/"
  run "cp support_functions.vim ~/.vim/snippets/"
end

def run(cmd)
  puts "Executing: #{cmd}"
  system cmd
end

# bugs:
# * comments in the .snippets file do not work
# * ugly
desc "convert single-file snippets to multi-file"
task :convert do
  raise "FILES required. Try rake convert FILES='~/.vim/snippets/*.snippets'" unless ENV['FILES']

  def write(filename, content)
    FileUtils.mkdir_p(File.dirname(filename)) unless File.exists?(File.dirname(filename))
    puts "writing #{filename}"
    File.open(filename, "w") { |f| f.print content }
  end

  Snippet = Struct.new(:trigger, :name, :type, :content)

  Dir[File.expand_path(ENV['FILES'])].each do |file|
    next unless file =~ /\.snippets/
    type = File.basename(file, '.snippets')
    puts "converting #{type}..."

    base_dir = File.expand_path("~/.vim/snippets/#{type}")
    FileUtils.mkdir(base_dir) unless File.exists?(base_dir) 

    content = File.read(file)
    chunks = content.split(/^snippet\s/)

    snippets = []

    chunks.each do |chunk| 
      # parse
      next if chunk.empty?
      lines = chunk.split(/\n/)
      lines.pop if lines.size > 1 && lines.last =~ /^#\s/ # get rid of comment # hackish and ugly

      s = Snippet.new
      lines.shift.strip =~ /^([^\s]+)\s?(.*)$/
      s.trigger = $1 
      s.name    = $2
      s.type = type
      s.content = lines.collect{|l| l.sub(/^\t/, '')}.join("\n") # strip leading tab

      snippets << s
    end

    # group for snippets with the same mapping
    groups = {}
    snippets.each do |snippet|
      groups[snippet.trigger] ||= []
      groups[snippet.trigger] << snippet
    end

    groups.each do |trigger, group|
      if group.size > 1
        group.each_with_index do |s,i|
          filename = File.join(base_dir, s.trigger, "#{s.name}.snippet")
          write(filename, s.content) 
        end
      else
        s = group.first
        filename = File.join(base_dir, s.trigger + ".snippet")
        write(filename, s.content)
      end
    end

  end
  
end
