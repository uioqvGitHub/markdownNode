```flow
st=>start: Start:>https://www.zybuluo.com
io=>inputoutput: verification:>https://www.zybuluo.com
op=>operation: Your Operation
cond=>condition: Yes or No?
sub=>subroutine: Your Subroutine
e=>end
st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```

