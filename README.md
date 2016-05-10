# senxiao.github.io
## Istall
$ npm install --save-dev gulp-rev<br>
$ npm install --save-dev gulp-rev-collector<br>
$ npm install --save-dev gulp-cdnizer
## Usage
```javascript
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
//其中生成了一个json文件，通过这个文件可以实现文件中引用文件名的替换
//css，js文件中引用的图片添加对应的md5码
gulp.task('replaceCss',['img',  'fonts', 'html','other'], function(){
	return gulp.src([path.join(conf.paths.dist,'assets/images/*.json'),path.join(conf.paths.dist,'/styles/*.css')])
	.pipe(revCollector())
	.pipe(gulp.dest(path.join(conf.paths.dist,'/styles')))
})
gulp.task('replaceJs',['img',  'fonts', 'html','other'], function(){
	return gulp.src([path.join(conf.paths.dist,'assets/images/*.json'),path.join(conf.paths.dist,'/scripts/*.js')])
	.pipe(revCollector())
	.pipe(gulp.dest(path.join(conf.paths.dist,'/scripts')))
})
gulp.task('build', ['img', 'fonts', 'cdn', 'other', 'replaceCss', 'replaceJs', 'jsCdn']);
```

