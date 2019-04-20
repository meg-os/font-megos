#
require 'rake/clean'
# require 'rake/packagetask'

PATH_SRC    = "fontx2/"
PATH_OUTPUT = "include/"

SRCS = []
SRCS << FileList["#{PATH_SRC}*.fnt"]
SRCS << FileList["#{PATH_SRC}*.FNT"]

CLEAN.include(FileList["#{PATH_OUTPUT}*.h"])
directory PATH_OUTPUT

desc "Defaults"
task :default => [PATH_OUTPUT, :make_font].flatten

def font_def(dest, src)
  name = File.basename(dest, '.*')
  CLEAN.include dest
  file dest => src do |t|
    puts "fontconv: #{dest} <= #{src}"
    bin = File.binread(t.prerequisites[0]).unpack('C*')
    font_w = bin[14]
    font_h = bin[15]
    font_w8 = (font_w+7)/8;
    bin.shift(17 + 32*font_w8*font_h)
    data = 96.times.map do
      (font_w8*font_h).times.map do
        '0x%02x' % bin.shift()
      end.join(',')
    end
    File.open(t.name, 'w') do |file|
      file.puts "// AUTO GENERATED #{name}.h"
      file.puts "static const int #{name}_w=#{font_w}, #{name}_h=#{font_h};"
      file.puts "static const uint8_t #{name}_data[] = {\n#{ data.join(",\n") }\n};"
    end
  end
end

dests = SRCS.flatten.map do |src|
  name = File.basename(src, ".*")
  dest = "#{PATH_OUTPUT}#{name}.h"
  font_def dest, src
  dest
end

task :make_font => [PATH_OUTPUT, dests].flatten
