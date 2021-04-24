# My Blog

#### 😃 Click [here](https://meyer99.github.io/) to visit my personal website  
#### 🚀 Powered by [Github Pages](https://pages.github.com/) and [Jekyll](https://jekyllrb.com/)  
#### 📢 Theme template provided by [Huxpro](https://github.com/Huxpro/huxpro.github.io)    

<br>

### Change Log
2021.4.24
* change mathjax javascript source to [link](https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-AMS-MML_SVG) (the previous one didn't work)  

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
