RUBY
====

    # Variables :

    my_num = 25
    my_bool = true # or false
    my_string = "Hello world!"

    # By convention, these variables should start
    # with a lowercase letter and words should be separated by underscores

    print my_string || print "#{my_string}" # use #{...} to print var inside ""

    # Operations :

    + - * / ** % && || ! == != < <= > >= += -= *= /=

    # Print commands :

    puts : print line with \n
    print : print line without \n
    gets : read input
    gets.chomp : read input and remove \n

    # String Methods :

    "My String".length
    "My String".reverse
    "My String".downcase
    "My String".upcase
    "My String".capitalize
    "My String".include? "pattern"
    "My String".gsub!(/s/, "th")

    # ! at the end of the method modify the object.

    my_var = "Hello"
    if my_var.length < 1
      puts "OK"
    else
      puts "KO"
    end

    # Comments :

    # ou =begin ... =end

    # If :

    if my_var < 0
      puts "Negative"
    elseif my_var > 0
      puts "Positive"
    else
      puts "Null"
    end

    my_var = "Hello"
    if my_var.length < 1
      puts "OK"
    else
      puts "KO"
    end

    # Loop :

    while ...
    	...
    end

    until ...
    	...
    end

    for num in 1...10 # count from 1 to 10 (3 dots)
    	...
    end

    for num in 1..11 # count from 1 to 10 (2 dots)
    	...
    end

    loop { print "Hello, world!" } # infinite loop

    i = 20
    loop do
      i -= 1
      next if i %2 != 0 # next skip end of loop if i is an event number
      print "#{i}"
      break if i <= 0 # break exit loop if i <= 0
    end

    10.times { print "ABCD" } # == for i in 1...10 { print "ABCD" }

    # Arrays :

    array = [1,2,3,4,5]
    array.each do |x|
      x += 10
      print "#{x}"
    end
