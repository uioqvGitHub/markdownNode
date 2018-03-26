```flow
st=>start: Start :>https://#
io=>inputoutput: verification :>https://#
op=>operation: Your Operation
cond=>condition: Yes or No?
sub=>subroutine: Your Subroutine
e=>end
st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```



![用户用例](http://yuml.me/diagram/scruffy/usecase/[user]-(Login), [user]-(Logout))