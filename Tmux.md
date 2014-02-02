TMUX
====

@http://doc.ubuntu-fr.org/tmux

<C-b>+<c> : Create a new window
<C-b>+<n> : Change to the next window
<C-b>+<X> : Select windows 0 to 9
<C-b>+<d> : Detach the current client
<C-b>+<,> : Rename the current window
<C-b>+<w> : Affiche Choose the current window interactively
<C-b>+<t> : Show the time
<C-b>+<Page Up> : Enter copy mode and scroll one page up

<C-b>+<«> : Split the current pane into two, top and bottom
<C-b>+<%> : Split the current pane into two, left and right
<C-b>+<o> : Select the next pane in the current window
<C-b>+<Alt> + (Up, Down, Left, Right) : Resize the current pane in steps of one cell
<C-b>+<!> : Break the current pane out of the window
<C-b>+<q> : Briefly display pane indexes

<C-b> then ":join" : allow to join a terminal in a split window
	args :
		-h or -v (horizontal or vertical)
		-s 3.0 terminal 3 and pane 0
		-p 50 percent of window
