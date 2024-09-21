---
tags:
  - "#bash"
  - "#scripting"
  - "#example"
---

base: [shell-bash-scripting](shell-bash-scripting.md)

# Bash Scripting Base Example

## Initial Script

Imagine you want to convert a pdf to a tif file and you use the following command:

``` bash
$ magick -density 300x300 foo.pdf -quality 90 -background white -alpha background -compress zip foo.tif
```

You are sick of writing it over and over. You decide to write a simple script by putting the command in a file, called `script.sh`:

``` bash
magick -density 300x300 foo.pdf -quality 90 -background white -alpha background -compress zip foo.tif
```

````` ad-note
title: What can be improved? What are the issues?

**System**
- the script only runs when you call it with the 'interpreter', here `bash`
	
	``` bash
	$ bash ./script.sh
	```
- You need to copy it all the time

**Structure**

- Not readable at a certain point
- No help
- No options
`````

# Let's talk about base structure

Let's create a base structure.

**Script Skeleton/Script Outline**

``` sh
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : foobar
# Version     : 0.1.0
# Description : Foo for Bar
# Required    : -
# Note        : -

#==========#
#== INIT ==#
#==========#

#================#
#== USER INPUT ==#
#================#

#===============#
#== FUNCTIONS ==#
#===============#

#---------------#

#==========#
#== MAIN ==#
#==========#
magick -density 300x300 foo.pdf -quality 90 -background white -alpha background -compress zip foo.tif
```

- Shebang line `#!` tells, which interpreter should be used for and in bash it runs in a subshell!
	```
	#!/usr/bin/env bash
	```
- Licensing
	```
	# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
	# SPDX-License-Identifier: MIT
	```
- Metablock
	```
	# Name        : foobar
	# Version     : 0.1.0
	# Description : Foo for Bar
	# Required    : -
	# Note        : -
	```
# Let's talk about System stuff

- Make your file executable
	``` bash
	$ chmod 744 script.sh
	```

- Add your file the either in standard place for executable or add the current spot to the system variable `PATH`
	``` bash
	$ echo $PATH
	$ PATH="$PWD:$PATH"
	```

# Building your script

## Add Setup Variables and User Input

``` bash
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : pdf2tif
# Version     : 0.1.0
# Description : Convert pdf to tif
# Required    : imagemagick
# Note        : -

#==========#
#== INIT ==#
#==========#
_SETUP_MAGICK_INPUT="\
-density 300x300
"

_SETUP_MAGICK_CONVERT="\
-quality 90 \
-background white \
-alpha background \
-compress zip"

#================#
#== USER INPUT ==#
#================#
_input=$1
_output=$2

#===============#
#== FUNCTIONS ==#
#===============#

#---------------#

#==========#
#== MAIN ==#
#==========#

magick $_SETUP_MAGICK_INPUT $_input $_SETUP_MAGICK_CONVERT $_output
```

- Now the following should run
	``` bash
	$ ./pdf2tif foo.pdf foo.tif
	```

## Add Base Functions and a HELP

``` bash
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : pdf2tif
# Version     : 0.1.0
# Description : Convert pdf to tif
# Required    : imagemagick
# Note        : -

#==========#
#== INIT ==#
#==========#
_VERSION='0.1.0'
_SCRIPT_NAME=$(basename "$0")

_SETUP_MAGICK_INPUT="\
-density 300x300
"

_SETUP_MAGICK_CONVERT="\
-quality 90 \
-background white \
-alpha background \
-compress zip"

#================#
#== USER INPUT ==#
#================#
_input=$1
_output=$2

#===============#
#== FUNCTIONS ==#
#===============#
# :Std. error message
_error_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 1
}

#--------------#

# :Std. message
_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 0
}

#--------------#

# :Help text
_printhelp() {
cat <<-EOM
foobar -- a foo toolbox
Usage: foobar COMMANDS FILE
Does Foo to bar

Requirements:
  - sweets and love

Example of usage:
  foobar --help

Options:
 -h   --help                print help message and exit

NOTE: none 
EOM
exit 0
}

#---------------#

# :Reads the input arguments
_argument_parser() {
  while [[ -n "$1" ]]; do
    case $1 in
      -h  | --help    ) _printhelp ;;
            --version ) _msg "Version $_VERSION" ;;
      --              ) shift; break ;;
      -*              ) _error_msg "Unknown option $1"; shift ;;
      *               ) break ;;
    esac
  done
}

#---------------#

#==========#
#== MAIN ==#
#==========#
_argument_parser $@
magick $_SETUP_MAGICK_INPUT $_input $_SETUP_MAGICK_CONVERT $_output
```

- You may improve the readability by placing the main routine in a function as well 
- AND you can easily add options in just one place, like `-shave 500x800`

``` bash
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : pdf2tif
# Version     : 0.1.0
# Description : Convert pdf to tif
# Required    : imagemagick
# Note        : -

#==========#
#== INIT ==#
#==========#
_VERSION='0.1.0'
_SCRIPT_NAME=$(basename "$0")

_SETUP_MAGICK_INPUT="\
-density 300x300
"

_SETUP_MAGICK_CONVERT="\
-quality 90 \
-background white \
-alpha background \
-compress zip"

#================#
#== USER INPUT ==#
#================#
_input=$1
_output=$2

#===============#
#== FUNCTIONS ==#
#===============#
# :Std. error message
_error_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 1
}

#--------------#

# :Std. message
_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 0
}

#--------------#

# :Help text
_printhelp() {
cat <<-EOM
foobar -- a foo toolbox
Usage: foobar COMMANDS FILE
Does Foo to bar

Requirements:
  - sweets and love

Example of usage:
  foobar --help

Options:
 -h   --help                print help message and exit

NOTE: none 
EOM
exit 0
}

#---------------#

# :Reads the input arguments
_argument_parser() {
  while [[ -n "$1" ]]; do
    case $1 in
      -h  | --help    ) _printhelp ;;
            --version ) _msg "Version $_VERSION" ;;
      --              ) shift; break ;;
      -*              ) _error_msg "Unknown option $1"; shift ;;
      *               ) break ;;
    esac
  done
}

#---------------#

# :Main function pdf2tif
_pdf2tif() {
  magick $_SETUP_MAGICK_INPUT $_input $_SETUP_MAGICK_CONVERT $_output
}

#==========#
#== MAIN ==#
#==========#
_argument_parser $@
_pdf2tif
```

## Add own User input

``` ad-warning
title: Now you want to add some functionality. What is a major issue with this script?
```

- The user input should be part of the argument parser!

``` bash
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : pdf2tif
# Version     : 0.1.0
# Description : Convert pdf to tif
# Required    : imagemagick
# Note        : -

#==========#
#== INIT ==#
#==========#
_VERSION='0.1.0'
_SCRIPT_NAME=$(basename "$0")

_SETUP_MAGICK_INPUT="\
-density 300x300
"

_SETUP_MAGICK_CONVERT="\
-quality 90 \
-background white \
-alpha background \
-compress zip"


#===============#
#== FUNCTIONS ==#
#===============#
# :Std. error message
_error_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 1
}

#--------------#

# :Std. message
_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 0
}

#--------------#

# :Help text
_printhelp() {
cat <<-EOM
foobar -- a foo toolbox
Usage: foobar COMMANDS FILE
Does Foo to bar

Requirements:
  - sweets and love

Example of usage:
  foobar --help

Options:
 -h   --help                print help message and exit

NOTE: none 
EOM
exit 0
}

#---------------#

# :Reads the input arguments
_argument_parser() {
  while [[ -n "$1" ]]; do
    case $1 in
      -h  | --help    ) _printhelp ;;
            --version ) _msg "Version $_VERSION" ;;
      --              ) shift; break ;;
      -*              ) _error_msg "Unknown option $1"; shift ;;
      *               ) break ;;
    esac
  done

#-- user input
_input=$1
_output=$2

}

#---------------#

# :Main function pdf2tif
_pdf2tif() {
  magick $_SETUP_MAGICK_INPUT $_input $_SETUP_MAGICK_CONVERT $_output
}

#==========#
#== MAIN ==#
#==========#
_argument_parser $@
_pdf2tif
```

- For addition user input you utilize the `_argument_parser` function AND
- You should add a default value for your input in the `INIT` section

``` ad-attention
title: Do not forget your help text.
```

``` bash
...
_density='300x300'
...

# :Help text
_printhelp() {
...
 -d   --density NUMxNUM     density of the pixels per inch, default 300x300
...

# :Reads the input arguments
_argument_parser() {
  while [[ -n "$1" ]]; do
    case $1 in
      -d  | --density ) _density=$2; shift 2 ;;
      -h  | --help    ) _printhelp ;;
            --version ) _msg "Version $_VERSION" ;;
      --              ) shift; break ;;
      -*              ) _error_msg "Unknown option $1"; shift ;;
      *               ) break ;;
    esac
  done

#-- user input
_input=$1
_output=$2

}
```

- Your settings are still initialized at the top, you need to set it via a function, let's say:
``` bash
# :Set magick input settings
_set_magick_input() {
  _setup_magick_input="\
  -density $_density
  "
}
```

- You may also add some error output
	```bash
	trap '_error_msg "Execution aborted."' 1 2 3 9 15
	```
- You also can add some error handling, for example checking if the input exist
	``` bash
	input="$1"
	extension="pdf"
	file_extension=`echo ${input##*.} | tr '[:upper:]' '[:lower:]'`

    if [[ ! -f $input ]] && [[ $input_extension != $extension ]]; then
      _error_msg "$input must be an existing $extension."
    fi
	```

``` bash
#!/usr/bin/env bash
# SPDX-FileCopyrightText: Copyright (c) 2024 Max Musterman
# SPDX-License-Identifier: MIT

# Name        : pdf2tif
# Version     : 0.1.0
# Description : Convert pdf to tif
# Required    : imagemagick
# Note        : -

#==========#
#== INIT ==#
#==========#
_VERSION='0.1.0'
_SCRIPT_NAME=$(basename "$0")
_density='300x300'



_SETUP_MAGICK_CONVERT="\
-quality 90 \
-background white \
-alpha background \
-compress zip"


#===============#
#== FUNCTIONS ==#
#===============#
# :Std. error message
_error_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 1
}

#--------------#

# :Std. message
_msg() {
  echo "$_SCRIPT_NAME: $1" >&2
  exit 0
}

#--------------#

# :Help text
_printhelp() {
cat <<-EOM
foobar -- a foo toolbox
Usage: foobar COMMANDS FILE
Does Foo to bar

Requirements:
  - sweets and love

Example of usage:
  foobar --help

Options:
 -h   --help                print help message and exit
 -d   --density NUMxNUM     density of the pixels per inch, default 300x300

NOTE: none 
EOM
exit 0
}

#---------------#

# :Reads the input arguments
_argument_parser() {
  while [[ -n "$1" ]]; do
    case $1 in
      -d  | --density ) _density=$2; shift 2 ;;
      -h  | --help    ) _printhelp ;;
            --version ) _msg "Version $_VERSION" ;;
      --              ) shift; break ;;
      -*              ) _error_msg "Unknown option $1"; shift ;;
      *               ) break ;;
    esac
  done

#-- user input

_set_input $1
_output=$2

}

#---------------#

# :Main function pdf2tif
_pdf2tif() {
  _set_magick_input
  magick $_setup_magick_input $_input $_SETUP_MAGICK_CONVERT $_output
}

# :Set magick input settings
_set_magick_input() {
  _setup_magick_input="\
  -density $_density
  "
}
# :Set input
_set_input() {
	local input="$1"
	local extension="pdf"
	local input_extension=`echo ${input##*.} | tr '[:upper:]' '[:lower:]'`
    
    if [[ ! -f $input || "$input_extension" != "$extension" ]] ; then
        _error_msg "$input must be an existing $extension with the $extension extension."
    else
        _input=$1
    fi
}

#==========#
#== MAIN ==#
#==========#
trap '_error_msg "Execution aborted."' 1 2 3 9 15
_argument_parser $@
_pdf2tif
```
