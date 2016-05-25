# NAME

App::Yath - Yet Another Test Harness, alternative to prove

# DESCRIPTION

This package implements the `yath` command line tool.

# SYNOPSIS

## COMMAND LINE

    yath [options] [files] [directories]

## IN A SCRIPT

### MINIMAL

    use App::Yath;
    my $yath = App::Yath->new(args => \@ARGV);
    my $exit = $yath->run();
    exit($exit);

This minimal version is all most people will need. However it is recommended
you use the complete form below. The complete form does a much better job of
backing out of stack frames in preload+fork mode. Using the minimal can cause
problems in some edge cases where tests assume there are no stack frames
outside the test script itself.

### COMPLETE

    use App::Yath;

    T2_DO_FILE: {
        my $yath = App::Yath->new(args => \@ARGV);
        my $exit = $yath->run();
        exit($exit);
    }

    my $file = $Test2::Harness::Runner::DO_FILE
        or die "No file to run!";

    # Test files do not always return a true value, so we cannot use require. We
    # also cannot trust $!
    package main;
    $@ = '';
    do $file;
    die $@ if $@;
    exit 0;

In preload+fork mode the runner will attempt to break out to the `T2_DO_FILE`
label. If the example above is inserted into your top-level script then the
script will be able to run with minimal stack trace noise.

# COMMAND LINE ARGUMENTS

## COMMON

- -I\[dir\] --include="dir"

    Add directories to `@INC`.

- -L\[Module\] --preload="Module"

    Add a module to preload. (Prefork)

- -R\[name\] --renderer="name"

    Add a renderer. `"Test2::Harness::Renderer::$NAME"` is implied. Prefix with
    '+' to give an absolute module name `"+My::Name"`.

- -S\[s=val\] --switch="s=val"

    Add switches to use when executing perl.

- -c\[n\] --color=n

    Override the default color level. (0=off)

- -h --help

    Show this usage help.

- -j\[n\] --jobs=n

    How many tests to run concurrently.

- -m --merge

    Merge STDERR and STDOUT from test files.

- -q --quiet

    Do not use the default renderer.

- -v --verbose

    Show every event, not just failures and diag.

- -x\[pattern\] --exclude=\[pattern\]

    Exclude any files that match the pattern.

## OTHER OPTIONS

- --parser=\[name\] --parser\_class=\[name\]

    Override the default parser. `"Test2::Harness::Parser::$NAME"` is implied.
    Prefix with '+' to give an absolute module name.

- --runner=\[name\] --runner\_class=\[name\]

    Override the default runner. `"Test2::Harness::Runner::$NAME"` is implied.
    Prefix with '+' to give an absolute module name.

# METHODS

- $yath = App::Yath->new(args => \\@ARGV)

    Create a new instance. Accepts an array of command line arguments.

- $arg\_ref = $yath->args()

    Args array passed into construction (was modified during construction.)

- $harness = $yath->harness()

    The [Test2::Harness](https://metacpan.org/pod/Test2::Harness) instance that will be used (configured during object
    construction).

- $files\_ref = $yath->files()

    The arrayref of files as processed/expanded from the command line arguments.

- $exclude\_ref = $yath->exclude()

    The arrayref of file exclusion patterns.

- $renderers\_ref = $yath->renderers()

    The arrayref of renders that are listening for events.

- $exit = $yath->run()

    Run the tests, returns an integer that should be used as the exit code.

- $yath->load\_module($prefix, $name)

    Used to load a module given in `$name`. `$prefix` is appended to the start of
    the module name unless `$name` begins with a '+' character.

- $files\_ref = $yath->expand\_files(@files\_and\_dirs)

    Takes a list of filea and directories and expands it into a complete list of
    test files to run.

- $yath->help()

    Prints the command line help and exits.

# SOURCE

The source code repository for Test2-Harness can be found at
`http://github.com/Test-More/Test2-Harness/`.

# MAINTAINERS

- Chad Granum <exodist@cpan.org>

# AUTHORS

- Chad Granum <exodist@cpan.org>

# COPYRIGHT

Copyright 2016 Chad Granum <exodist7@gmail.com>.

This program is free software; you can redistribute it and/or
modify it under the same terms as Perl itself.

See `http://dev.perl.org/licenses/`
