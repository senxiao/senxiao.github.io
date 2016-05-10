# senxiao.github.io
## Istall
$ npm install --save-dev gulp-rev<br>
$ npm install --save-dev gulp-rev-collector<br>
$ npm install --save-dev gulp-cdnizer
## Usage
```
var rev = require('gulp-rev'); 
var revCollector = require('gulp-rev-collector');   
//图片添加md5码
gulp.task('img',function(){
	return gulp.src(path.join(conf.paths.src,'/assets/images/*.{gif,png,jpg,jpeg}'))
	.pipe(rev())
	.pipe(gulp.dest(path.join(conf.paths.dist,'/assets/images')))
  .pipe(rev.manifest())
  .pipe(gulp.dest(path.join(conf.paths.dist,'/assets/images')));
});
```

