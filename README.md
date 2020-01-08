## Walkthrough of VIM

### How to get the most out of your text editor

#### Extensibility 
make sure you are familier enough with your editor to extend it as is required for your work flow.
In vim we have:
- Plugins: (Plug | `:PlugInstall`, `:PlugClean`)
```
# Install Plug for plugin management
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
        https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```
- Vim Script

```
" Toggle between vertical and horizontal layout
let g:vert = 0
fun! PANETOG()
    if g:vert
        call PANEVERT()
        let g:vert=0
    else
        call PANEHOR()
        let g:vert=1
    endif
endfun
nnoremap <C-y> :call PANETOG()<CR>

```
- language bindings (python, ruby, etc.)


#### Make sure your extensions are portable
- All plug modifications go in your `.vimrc`

```
" Tpope Plugins
" vim verb on surrouding operators
Plug 'tpope/vim-surround' 
" Add plugin support for . operator
Plug 'tpope/vim-repeat' 
" gc is now verb, gc3j comment out 3 lines down
Plug 'tpope/vim-commentary' 
" Commentary Changes
autocmd FileType c setlocal commentstring=//\ %s

```
- Version control your text editor extensions

```
git clone https://github.com/ethanjwright/dotfiles
```

- Make the editor conform to your work flow, explore the key mappings and add them when they don't exist

```
" King of all remaps
ino jk <esc>


" VIM session handling: :S sessionname to save your current session 
" ( doesn't save files ) | from outside of vim : s sessionname to open session
function MakeSession(session)
    execute "mks! ". fnameescape("~/.vim/sessions/" . a:session . ".vim")
    execute "qa"
endfunction
command! -nargs=1 S call MakeSession(<f-args>)
```


#### Learn the language: Vim is Noun, Verb, Modifier interactions
- Verbs
  - v (visual) 
    - visual allows you to update entire
    - block of code or even
    - increment groups of numbers
    - engage through ctrl, or use w/ noun modifier
  - c (change)
    - delete noun, leave you in insert
  - d (delete)
    - delete noun, leave you in normal

- Modifiers ( Examples w/ change verb, i.e. ci" )
  - i (inside)  -- "Some sample text to change"
  - a (all)     -- "More sample text, also remove quotes"
  - t (till)    -- "Delete the first bit here, leave the rest".
  - f (find)    -- "Leave the first bit, leave the second chunk."
    - capitals tend to go the opposite direction, i.e. f ->  & F <-

- Objects ( the thing you want the verb/modifier to operate on  )
  - w (word)       -- "Delete delete the repeated word."          | daw
  - symbols ('', "",(), {}, []) -- "change" (a > b) { return }    | ci", ci(, di{
  - line numbers                                                  | 
    - something 0 line below                                      | d2j
    - something 1 lines below
    - something 2 lines below
  - p (paragraph)  --                                             | dap

  ```

     def hello_world(name)
        print(f'Hello, {name}')
        print(f'exiting function')

  ```

#### Movement -- hjkl is a thing of the past!

```
" in ~/.vimrc
set number relativenumber " or :set nu rnu
```

##### Direction for movement is also a verb!

In vim, modifying text and navigating text uses the exact same language. 
If you know how to delete from your cursor 20 lines down, you also know how to navigate 20 lines down. 
If you can delete from your cursor to the next comma, you can navigate there.
- Jump by relative line (5j)
- Jump to the first occurance of a character ( like into parens )    | f(
- tab forward/back between locations                                 | <CTRL-o> <CTRL-i>
- Jump to last edited line                                           | <g-;>
- jump to the top of the file                                        | gg
- jump to the bottom of the file                                     | G
- searching for known strings, / goes down, ? goes up:
  - ?top, /macros, n jumps to next occurance


#### Macros and Registers!

- Most friendly to use register is '.', it stores the last chunk you ran while in insert mode
- Try to write everything to be repeatable through the '.' register!

Change name -> user with: ci{user 
go to brackets, use . to repeat

```
" The variable is {name}"
" I'll use {name} + other"
" if {name} == John Smith"
```

Try the same using a macro saved in a register:
qsci{name<Esc>q
now press @s in every bracket

```
   "The variable is {name}"
   "I'll use {name} + other"
   "if {name} == John Smith"

```

Macros in pattern:
`:g/someRand/norm! @a`
```
   "The variable is {someRandomVariable}"
   "I'll use {someRandomVariable} + other"
   "if {someRandomVariable} == John Smith"

```

#### Reading / Writing from buffers
Data can also be saved in buffers. Most recent save by default goes in 0 buffer. | yy over this line
0p (paste from 0 buffer)

Or save to a named buffer | "ayy
" <- open registers 
a <- select 'a' named register
yy <- yank entire line

Now paste from named buffer | "ap


### Interacting with multiple windows:
- any open file creates a buffer
- you may have one buffer open in many windows
- you may open many buffers in many windows

```
   " navigate <C-w hjkl>
   :sp " create a horizontal split of current open buffer
   :vs " create a vertical split of current open buffer
   :vs tmp " create a vertical split with a new buffer, a file called tmp

   " closing panes
   :qa " quit all
   :wa " save all
```


#### VIM Golf!

Change the 'Hello world' -> 'Hello, {name}' using the dot operator

```

import sys

name = 'John Smith'
print(f'after sys Hello world!')

def main(argv):
    print(f'Hello world!')
    pass


if __name__ == "__main__":
    print(f'Hello world!')
    main(sys.argv)

```

Change every conditional from if ( user.has_cats ) to if ( user.has_cats && user.has_dogs )

```

def check_good(user):
  if ( user.has_cats ):
    print(f'You are a good person')

def check_bad:
  if ( user.has_cats ):
    return
  print(f'you are a bad person')

```

Record a macro yourself for a more complex operation

q<a-z> <-- save the macro
"<a-z> <-- use the macro

Add initializers to an enum (*hint U changes highlighted text from lower to upper case)

Start File

```

const enum TestkitType {
  vanilla,
  unidriver,
  protractor,
  puppeteer,
  unknown,
}

```

End File 

```

const enum TestkitType {
  vanilla = "VANILLA",
  unidriver = "UNIDRIVER",
  protractor = "PROTRACTOR",
  puppeteer = "PUPPETEER",
  unknown = "UNKNOWN",
}

```

### Extras!

#### Modify several lines

Remove identical text at the beginning of several lines and the closing parenthesis

Start File:

```
Assert.ThrowsAsync<Exception>(() => _auction.StartSellingItem());
Assert.ThrowsAsync<Exception>(() => _application.StartBiddingIn(_auction));
Assert.ThrowsAsync<Exception>(() => _auction.HasReceivedJoinRequestFromSniper());
Assert.ThrowsAsync<Exception>(() => _auction.AnnounceClosed());
Assert.ThrowsAsync<Exception>(() => _application.ShowsSniperHasLostAuction());
```

End File:

```
_auction.StartSellingItem();
_application.StartBiddingIn(_auction);
_auction.HasReceivedJoinRequestFromSniper();
_auction.AnnounceClosed();
_application.ShowsSniperHasLostAuction();
```

#### 


##### Difficult: Transpose two arrays into one

Start file:

```
[1,2,3,4,5,6,7,8,9,0]
[a,b,c,d,e,f,g,h,i,j]
```

End File:

```
[1,a,2,b,3,c,4,d,5,e,6,f,7,g,8,h,9,i,0]
```
