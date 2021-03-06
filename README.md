Logging library for vim
=======================

This is a slightly modfied clone of
[log.vim](https://www.vim.org/scripts/script.php?script_id=2330) by
[Yukihiro Nakadaira](https://github.com/ynkdir/).

Installation
------------

Put this file in autoload directory in your 'runtimepath'.

The easiest to do this is using the plugin mechanism of vim 8 or a vim plugin
manager.

Usage
-----

```vim
" in .vimrc
call log#init('ALL', ['/dev/stdout', '~/.vim/log.txt'])

" in script
let s:log = log#getLogger(expand('<sfile>:t'))

function s:func()
  call s:log.trace('start of func()')
  if s:log.isDebugEnabled()
    call s:log.debug('debug information')
  endif
  call s:log.trace('end of func()')
endfunction

" If you want to distribute your script without log.vim, use :silent! or
" exists().
silent! let s:log = log#getLogger(expand('<sfile>:t'))
function s:func()
  silent! call s:log.info('aaa')
  if exists('s:log')
    call s:log.info('bbb')
  endif
endfunction
```

Options
-------

There is only one option for the default format string:
```vim
" optionally set a default format string
let g:log_format = "{strftime(\"%Y-%m-%d %H:%M:%S\")} {plevel} [{name}] {msg}"
```

This overrides the default hardcoded format string.

This option may still be overriden with a different format string when
calling the `log#init()` method.

Reference
---------

### log#init(…)
```
function log#init(level, targets [, format [, filter]])
@param level [String]
  Log level.  One of 'ALL|TRACE|DEBUG|INFO|WARN|ERROR|FATAL|NONE'.
  For example, when level is 'WARN', output of log.warn(), log.error() and
  log.fatal() will appear in log.
@param targets [mixed]
  Output target.  Filename or Function or Dictionary or List of these
  values.
  Filename:
    Log is appended to the file.
  Function:
    function Log(str)
      echo a:str
    endfunction
  Dictionary:
    let Log = {}
    function Log.__call__(str)
      echohl Error
      echo a:str
      echohl None
    endfunction
@param format [String]
  Log format.  {expr} is replaced by eval(expr).  For example, {getpid()}
  is useful to detect session.  Following special variables are available.
  {level}   log level like DEBUG, INFO, etc...
  {plevel}  like {level} but right padded with spaces to be always 5 characters long
  {name}    log name specified by log#getLogger(name)
  {msg}     log message
  If this is 0, '', [] or {} (empty(format) is true), default is used.
  default:  [{level}][{strftime("%Y-%m-%d %H:%M:%S")}][{name}] {msg}
@param filter [mixed]
  Pattern (String) or Function or Dictionary to filter log session.
  Filter is applied to name that specified by log#getLogger(name).  If
  result is false, logging session do not output any text.
  Pattern (String):
    name =~ Pattern
  Function:
    function Filter(name)
      return a:name == 'mylib'
    endfunction
  Dictionary:
    let Filter = {}
    let Filter.filter = ['alib', 'blib', 'clib']
    function Filter.__call__(name)
      return index(self.filter, a:name) != -1
    endfunction
@return void
```

### log#getLogger(…)
```
function log#getLogger(name)
@param name [String] Log name
@return Logger object
```
