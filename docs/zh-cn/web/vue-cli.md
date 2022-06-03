# Vue-cli


## vue-cli4 optimazition

### gzip compress

- nginx 开启 gzip

```bash
gzip on;
  gzip_vary on;
  gzip_min_length 1000;
  gzip_comp_level 2;
  gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml image/jpeg image/gif image/png application/javascript; 

```

- vue-cli4 [webpack4 ] compression-webpack-plugin 
> compression-webpack-plugin 6.1 


### Vue Optimization

