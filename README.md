# My Blog

#### 😃 Click [here](https://meyer99.github.io/) to visit my personal website  
#### 🚀 Powered by [Github Pages](https://pages.github.com/) and [Jekyll](https://jekyllrb.com/)  
#### 📢 Theme template provided by [Huxpro](https://github.com/Huxpro/huxpro.github.io)    

<br>

### Change Log
2020.11.21
* fix mathjax display mode rendering problem

In `_includes/mathjax_support.html`    
Old: 
```html
tex2jax: {
    inlineMath: [ ['$','$'] ],
    displayMath: [ ['$$','$$'] ],
    processEscapes: true,
}
```

New:
```html
tex2jax: {
    inlineMath: [ ['$','$'], ["\\(","\\)"] ],
    processEscapes: true,
}
```
