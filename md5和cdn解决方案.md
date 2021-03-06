# md5和cdn解决方案
```
需要解决的问题：
1、资源的唯一性，以便当文件内容发生变化时，cdn服务器会自动从本地加载新文件
2、给引用的资源添加cdn前缀
解决方案：
1、通过rev和rev-collector给js，css，img文件添加md5码，并在引用中添加相应的md5码
2、通过cdnizer在引用资源的路径中添加cdn前缀
```

## Install
$ npm install --save-dev gulp-rev<br>
$ npm install --save-dev gulp-rev-collector<br>
$ npm install --save-dev gulp-cdnizer

## Usage
### addMd5
图片添加md5码并将文件中引用的图片文件名做相应更改，也可以用此方法给js，css文件添加md5码

```javascript
var rev = require('gulp-rev'); 
var revCollector = require('gulp-rev-collector');   
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

###addCdn
给引用的资源路径添加md5前缀
给js中引用的图片添加cdn,css中引用的图片路径在线上环境下会自动添加cdn前缀，因此不需要进行操作

```javascript
//html加载的css，js文件添加cdn前缀
var cdnizer = require("gulp-cdnizer");
gulp.task('cdn', ['html'], function () { 
	gulp.src(path.join(conf.paths.dist, '/index.html'), function(err,files){
	}).pipe(cdnizer({
			defaultCDNBase: '//my.cdn.url:9528/',//需要添加的cdn前缀
	 		files: ['**/*.{js,css}']//需要添加cdn前缀的路径的文件类型
		}		
	)).pipe(gulp.dest(path.join(conf.paths.dist,'/')));	
	return true;
});
gulp.task('jsCdn',['html','replaceJs'],function(){
	gulp.src(path.join(conf.paths.dist,'/scripts/*.js'))
	.pipe(cdnizer({
		defaultCDNBase: '//my.cdn.url:9528/',	
		files: ['**/*.{gif,png,jpg,jpeg}']
	}))
	.pipe(gulp.dest(path.join(conf.paths.dist,'/scripts')))
})
```

### 注意事项：
	cdnizer无法给相对路径添加cdn前缀，需要将路径前面的../替换成cdn
	流程示例：代码合并=>压缩=>添加md5=>添加cdn
	每个部分的操作要依赖于前一个操作，即按顺序一个个执行
