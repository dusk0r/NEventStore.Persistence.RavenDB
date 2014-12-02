# encoding: utf-8
require 'albacore'
require 'rake/clean'
require 'fileutils'

## Config #####
dir_basedir = File.dirname(__FILE__)
dir_build = File.join(dir_basedir, "build")
dir_tools = File.join(dir_basedir, "bin")
dir_artifacts = File.join(dir_basedir, "artifacts")

## Dir Tasks #####
directory dir_build
directory dir_artifacts

## Enviroment #####
#versionNumber = ENV['BUILD_NUMBER'] || '0.0.0.0'

## Tools #####
bin_xunit = File.join(dir_tools, 'xunit.runners.1.9.1', 'tools', 'xunit.console.clr4.exe')
bin_nuget = File.join(dir_basedir, 'src', '.nuget', 'NuGet.exe')

## Clean Tasks #####
CLOBBER.include(dir_artifacts)
CLEAN.include(dir_build)

desc "Run Tests"
task :test do
	sh "#{bin_xunit} #{File.join(dir_build, 'NEventStore.Persistence.RavenDB.Tests.dll')}"
end

desc "Compiles"
msbuild :build => [ :clean, dir_build ] do |msb|
  msb.properties = {
	:configuration => :Release,
	:outdir => "#{dir_build}/"
  }
  msb.targets [ :Clean, :Build ]
  msb.solution = "src/NEventStore.Persistence.RavenDB.sln"
end

desc "Creates NuGet Package"
task :nuget => dir_artifacts do
	sh "#{bin_nuget} pack NEventStore.Persistence.RavenDB.nuspec"
	Dir['*.nupkg'].each do |f|
		target = File.join(dir_artifacts, f)
		FileUtils.mv f, target
		puts "##teamcity[publishArtifacts '#{target}']"
	end
end