Awesome ZSH
===========
This document briefly covers ZSH shell and serves as a quick summary of its many features. It's not a full guide nor a detailed documentation. It's merely a scratchpad from my journey into ZSH. 

*Document assumes moderate knowledge of Bash shell.*

## Enable ZSH

	$ chsh -s /bin/zsh         # setting zsh as default shell
	                           # or simply use oh-my-zsh installer
				   
				   
## Getting to know the `.z` files 
What should/shouldn't go in .zshenv, .zshrc, .zlogin, .zprofile, .zlogout?
Here a list of what each file should/shouldn't contain. [reference](https://unix.stackexchange.com/questions/71253/what-should-shouldnt-go-in-zshenv-zshrc-zlogin-zprofile-zlogout).

1. **.zshenv** : (Read every time)

This file is always sourced, so it should set environment variables which need to be updated frequently. PATH (or its associated counterpart path) is a good example because you probably don't want to restart your whole session to make it update. By setting it in that file, reopening a terminal emulator will start a new Zsh instance with the PATH value updated.

But be aware that this file is read even when Zsh is launched to run a single command (with the -c option), even by another tool like make. You should be very careful to not modify the default behavior of standard commands because it may break some tools (by setting aliases for example).

2. **.zprofile** (Read at login)

Treat this file like `.zshenv` but for commands and variables which should be set once or which don't need to be updated frequently:

* environment variables to configure tools (flags for compilation, data folder location, etc.)
* configuration which execute commands (like SCONSFLAGS="--jobs=$(( $(nproc) - 1 ))") as it may take some time to execute.
If you modify this file, you can apply the configuration updates by running a login shell:

```
exec zsh --login
```
3. **.zshrc** (Read when interactive)

Put here everything needed only for interactive usage:

* prompt,
* command completion,
* command correction,
* command suggestion,
* command highlighting,
* output coloring,
* aliases,
* key bindings,
* commands history management,
* other miscellaneous interactive tools (auto_cd, manydots-magic)...


4. **.zlogin** (Read at login)

This file is like `.zprofile`, but is read after `.zshrc`. You can consider the shell to be fully set up at `.zlogin` execution time

So, use it to launch external commands which do not modify shell behaviors (e.g. a login manager).

5. **.zlogout** (Read at logout; Within login shell)

Here, you can clear your terminal or any other resource which was setup at login.

#### How to choose where to put a setting
* if it is needed by a command run non-interactively: .zshenv
* if it should be updated on each new shell: .zshenv
* if it runs a command which may take some time to complete: .zprofile
* if it is related to interactive usage: .zshrc
* if it is a command to be run when the shell is fully setup: .zlogin
* if it releases a resource acquired at login: .zlogout



## Completions

	% autoload -U compinit && compinit
	% cp -<TAB>
	% kill <TAB>

## ZSH on steroids (oh-my-zsh installed)

 - Install oh-my-zsh from [github](https://github.com/robbyrussell/oh-my-zsh)
 - Update configuration to your needs in `~/.zshrc`
 - Put custom configuration in `~/.oh-my-zsh/custom`

## Extended globing & qualifiers

	% echo *        # like in bash
	% echo **/*     # glob recursively
	% echo **/*(.)  # glob recursively (just files)
	$ echo **/*(/)  # glob recursively (just directories)

## Redirects

	% echo Hello >a.txt >&1         # print Hello to both a.txt and stdout
	% echo Hello >a.txt >/dev/fd/0  # same
	$ echo hello | tee a.txt        # same thing in bash
	% echo Hello >a.txt >b.txt      # print Hello to both a.txt and b.txt

## Auto change directories

	~% coding
	coding% 

	% unsetopt auto_cd      # to disable

## Expand paths

	~% cd c/w/m/i<TAB>
    ~% cd coding/work/mobile/ios

but if path is not unique:

	~% cd c/w<TAB>
	~% cd coding/w
	work  week  wrong

## Path replacement

	~% cd /usr/local/bin
	/usr/local/bin% cd bin share
	/usr/local/share%

## Expand variables

	~% echo %PATH<TAB>
	~% echo /opt/local/bin:/opt/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin

	% vared PATH         # load PATH variable into line editor, ENTER to accept

## Approximated completion

    # ~/.oh-my-zsh/custom/completions.zsh
    # Allow aproximation when completing
    zstyle ':completion:::::' completer _complete _approximate
    zstyle ':completion:*:approximate:*' max-errors 2

then try it out:

	~% cd codign/wo<TAB>
    ~% cd coding/work/

	~% cp --reg<TAB>
	~% cp --recursively

## SSH remote hosts

	~% ssh user@m<TAB>
    ~% ssh user@myserver.com

## Remote path completion

	~% git clone myserver.com:/home/user/<TAB>
    ~% git clone myserver.com:/home/user
    repo-a.git  repo-b.git  repo-c.git

## Iterating over files

	$ for f in *.txt; do echo $f; done  # bash
	% for f (*.txt) { echo $f; }        # same thing in zsh

## Renaming

	% autoload -U zmv                    # load zmv first
	% zmv '(*).txt' '$1.html'
	% zmv -n '(*).txt' '$1.html'
	% zmv -n '(**/)(*).txt' '$1/$2.html'

More examples at [zshwiki](http://zshwiki.org/home/builtin/functions/zmv).

## History navigation

	% zmv <UP>      # it will only iterate over history elements starting with zmv
	
## Better history navigation
If you have oh-my-zsh installed then enable ``history-substring-search`` plugin (``~/.zshrc``):

    plugins=(history-substring-search ...)	
    
You'll get even better context history navigation (with colors).

## Locating stuff
	
	% which echo ls sh
	% whence echo ls sh
	% echo =ls	

## Various Prompts

Modify `~/.zshrc` from:

	ZSH_THEME="robbyrussell"

to

	ZSH_THEME="random"

Each new zsh will get random theme. You can see them all at [wiki](https://github.com/robbyrussell/oh-my-zsh/wiki/themes).

## Custom hooks

    # ~/.oh-my-zsh/custom/utils.zsh
    # directory change hook
    #
    function chpwd; {
        if [[ "$PWD" == "/etc" ]]; then
    	    echo "You have entered /etc, don't break anything!"
        fi
    }
	
Then try it out:	
	
	~% cd /etc
    You have entered /etc, don't break anything!
    /etc%	


# Reference
1. [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
2. [ZSH Tips by ZZapper](http://www.rayninfo.co.uk/tips/zshtips.html)
3. [10 zsh tricks you may not know...](http://chneukirchen.org/blog/archive/2008/02/10-zsh-tricks-you-may-not-know.html)
4. [ZSH documentation](http://zsh.sourceforge.net/Guide/zshguide.html)
5. [Advanced Bash programming](http://www.tldp.org/LDP/abs/html/)
6. [Shell programming with bash: by example, by counter-example](http://matt.might.net/articles/bash-by-example/)

# License

Document released under [Attribution-NonCommercial-ShareAlike 4.0 International](http://creativecommons.org/licenses/by-nc-sa/4.0/deed.en_US). In other words: noncommercial, requires attribution and must be reproduced with a similar license.
