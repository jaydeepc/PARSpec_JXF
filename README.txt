PARSpec_JXF
===========

The CustomFormatter helps to create a Junit Type XML for parallel_rspec and rspec runner

The repo has two files that you need to keep in your root folder.

In your rake file, create a task like this:

  task :report_gen, :arg1 do |t, args|
    `ruby jd_parser.rb #{args[:arg1]}`
  end

This task should run after all the other rake tasks you are running. So make the other tasks depandant on this task. Example:

  task :'ci:smoke' do
    begin
      start_time = Time.now
      execute_commands <<-EOC
        parallel_rspec -n 4 spec/smoke.rb -o '--require ./jd_format.rb  --format CustomFormatter'
      EOC
    ensure
      total_exe_time = Time.now - start_time
      Rake::Task[:report_gen].invoke("#{total_exe_time}")
    end
  end

Note that you need to capture the start and end time to calculate the total time. The reason is, I have not found a smarter way
yet to caluclate the total time when tests are running in different threads. :o)

The xml created inside the reports/junit.xml looks like this as of now:

<testsuite tests="3" failures="0" errors="0" skipped="0" time="34.61261">
  <testcase name="verifies default home page has feedback section" result="PASSED"></testcase>
  <testcase name="Verify that the column name is displayed when the column is empty" result="PASSED"></testcase>
  <testcase name="Verify that the columns in a ticket are displayed in the correct order" result="PASSED"></testcase>
</testsuite>


NOTE: This is in initial stage, and I am working on providing some more details to the XML.
