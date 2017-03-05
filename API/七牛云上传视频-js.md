七牛云上传视频-js


准备：

七牛云账号，解析好的备用域名



前台：

```php
    <style type="text/css">
        .type2{display: none;}
        #progress{position:relative; margin-left:144px; padding: 0 20px; width:340px; height: 80px; display:none; background: #ededed;} 
		#bar {background-color: #D0333A; display:block; width:0%; height:10px; margin-bottom: 5px; border-radius:3px; color: #666; text-align: center; line-height: 30px; border-radius: 5px;}
		#form1{display: none;}
		#progress-bar{color: #9F0E15; line-height: 24px;}
		#quxiao-btn{height: 24px; padding: 0 5px; background: green; color: #fff;}
		#uploadvideo{ background: #55A348; border-radius:0;}
    </style>
    <div class="fabu">
        <form method="post" action="{:U('Home/Train/fabu')}"  id="fabuForm" enctype="multipart/form-data" onsubmit="return jQuery(this).validationEngine('validate');">
        <input type="hidden" value="{$data_t['t_id']}" name="teacher_id">
        <input type="hidden" id="t-video" name="t_video">
        <div>
			<label class="label-text">课程标题：</label><input type="text" name="t_name" class="validate[required,maxSize[100]]"/>
		</div>
		</form>
		<form id="form1">
			<div><label class="label-text" for="">上传音视频区：</label>
			<button type="button" style="padding-left: 10px;display: inline-block;" id="uploadvideo" class="tijiao-btn">选择文件</button>
			</div>
			<div id="progress">
				<div class="wd-h30">文件大小：<span id="filesize"></span></div>
				<div id="bar"></div>
				<span id="progress-bar"></span>　　<button type="button" id="quxiao-btn">取消上传</button>
			</div>
			<!--<div>
				<label class="label-text" for=""></label>
        		&nbsp;<button type="button" id="upload-vb" class="tijiao-btn">上传</button>
    		</div>-->
    		<p style="padding-left: 150px;color: #9F0E15;">
    		上传须知：<br/>
			文件大小：不支持断点续传，最大不超过{$config['max_upload_video_size']}M<br/>
			允许格式：mp4，rmvb，wmv，avi，mp3<br/>
			禁止发布：不得上传未经授权的他人作品，以及色情，反动等违法内容。
    		</p>
		</form>
			<div>
        		<button type="button" onclick="$('#fabuForm').submit()" id="z-tijiao" class="tijiao-btn wd-fr">提交</button>
    		</div>
    </div>
</div>
<div class="am-modal am-modal-alert" tabindex="-1" id="my-alert">
  <div class="am-modal-dialog">
    <div class="am-modal-hd">温馨提示</div>
    <div class="am-modal-bd" id="tips-text">
      	
    </div>
    <div class="am-modal-footer">
      <span class="am-modal-btn">我知道了</span>
    </div>
  </div>
</div>

<script src="/Skin/public/My97datepicker/WdatePicker.js" charset="utf-8"></script>
<script>
    $(function(){
	    $('#quxiao-btn').on('click', function(){
		    uploader.stop();
		    uploader.refresh();
		    $('#progress').hide();
		    $('#bar').css('width',0)
	    })
    });
	    
</script>
<!--引入jssdk-->
<script type="text/javascript" src="/Skin/public/plupload/js/plupload.full.min.js"></script>
<script type="text/javascript" src="/Skin/public/js-sdk/dist/qiniu.js"></script>
<script>
function changetype(key, type){
	$.ajax({ 
        url: '{:U('Home/Qiniu/change')}', 
        type: 'POST', 
        data: {key:key, type: type}, 
        timeout: 10000, 
        dataType: 'JSON', 
        success: function(data) {
	        //console.log(data)
            if(data.status == 1){
	            $('#t-video').val(data.video);
	            $('#bar').css('width',294);
	            $('#progress-bar').html('98%');
            	fileSize = $('#uploadvideo').attr('data-size');
            	setTimeout(function(){
                	tips('上传成功！');
                	$('#progress-bar').html('上传完成！')
            	}, parseInt(fileSize)*5);
            }else{
	            tips(data.info);
            }
            $('#z-tijiao').removeAttr('disabled');
        	$('#uploadvideo').attr('disabled','disabled');
        },
        error: function(err){
            if(err.statusText != 'abort') tips('转码失败！');
            $('#z-tijiao').removeAttr('disabled');
        },
        
    }); 
}


var uploader = Qiniu.uploader({
    runtimes: 'html5,flash,html4',      // 上传模式，依次退化
    browse_button: 'uploadvideo',         // 上传选择的点选按钮，必需
    uptoken : '{$token}', // uptoken是上传凭证，由其他程序生成
    // uptoken_url: '/uptoken',         // Ajax请求uptoken的Url，强烈建议设置（服务端提供）
    // uptoken_func: function(file){    // 在需要获取uptoken时，该方法会被调用
    //    // do something
    //    return uptoken;
    // },
    multi_selection: false,
    get_new_uptoken: false,             // 设置上传文件的时候是否每次都重新获取新的uptoken
    // downtoken_url: '/downtoken',
    // Ajax请求downToken的Url，私有空间时使用，JS-SDK将向该地址POST文件的key和domain，服务端返回的JSON必须包含url字段，url值为该文件的下载地址
    // unique_names: true,              // 默认false，key为文件名。若开启该选项，JS-SDK会为每个文件自动生成key（文件名）
    // save_key: true,                  // 默认false。若在服务端生成uptoken的上传策略中指定了sava_key，则开启，SDK在前端将不对key进行任何处理
    domain: '自定义域名',     // bucket域名，下载资源时用到，必需
    container: 'form1',             // 上传区域DOM ID，默认是browser_button的父元素
    max_file_size: '500mb',             // 最大文件体积限制
    flash_swf_url: '/Skin/public/plupload/js/Moxie.swf',  //引入flash，相对路径
    max_retries: 3,                     // 上传失败最大重试次数
    dragdrop: true,                     // 开启可拖曳上传
    drop_element: 'form1',          // 拖曳上传区域元素的ID，拖曳文件或文件夹后可触发上传
    chunk_size: '4mb',                  // 分块上传时，每块的体积
    auto_start: true,                   // 选择文件后自动上传，若关闭需要自己绑定事件触发上传
    filters : [ {title : "音频文件", extensions : "mp3"}, {title : "视频文件", extensions : "mp4,avi,rmvb,wmv"} ],
    init: {
        'FilesAdded': function(up, files) {
            plupload.each(files, function(file) {
	            fileSize = files[0].size;  
		        var fileName = files[0].name;   
		        var fileType = fileName.substr(fileName.lastIndexOf(".") + 1).toLowerCase();
		        window.fileType = fileType; 
		        $('#uploadvideo').attr('data-size',Math.floor(parseInt(fileSize)/100000));

			    var size = fileSize / 1024 / 1024;
		        
		        $('#filesize').html(size.toFixed(2)+'M')
                if (up.files.length <= 1) {
                    return;
                }
                up.removeFile(file); 
                
            });
        },
        'BeforeUpload': function(up, file) {
            // 每个文件上传前，处理相关的事情
            $('#progress').show();
            $('#z-tijiao').attr('disabled','disabled');
        },
        'UploadProgress': function(up, file) {
            // 每个文件上传时，处理相关的事情
            var percent = file.percent;
            jindu = percent > 97 ? 97 : percent;
            $('#bar').css('width',jindu*3)
            $('#progress-bar').html(jindu+'%')
        },
        'FileUploaded': function(up, file, info) {
	        console.log(info)
	        var res = JSON.parse(info);
	        $('#t-video').val(res.key);
        	changetype(res.key, fileType)
        	$('#quxiao-btn').hide();
        	$('#upload-vb').hide();
        },
        'Error': function(up, err, errTip) {
	        console.log(err)
	        
	        if(err.code == -601){
    	        tips('请上传mp4，rmvb，wmv，avi，mp3格式文件！');
	        }else{
    	        tips('上传失败！');
	        }
            
        },
        'UploadComplete': function() {
               //队列文件处理完毕后，处理相关的事情
        },
        'Key': function(up, file) {
            var key = "{$mid}_"+(new Date()).valueOf()+"."+fileType;
            window.key = key;
            // do something with key here
            return key
        }
    }
});

</script>
```

服务器端：

```php
<?php
// 引入鉴权类 引入上传类
use Qiniu\Auth;
use Qiniu\Storage\UploadManager;
use Qiniu\Storage\BucketManager;
use Qiniu\Processing\PersistentFop;

class QiniuAction extends AppbaseAction {
    public function change(){
	    //防止盗用
	    if(session('is_org') == ''){
		    echo '哈哈哈！';
		    die();
	    }
	    //ini_set("max_execution_time", 1000000000); 

	    $return = array();
	    $return['status'] = 0;

		$key = I('key');
	       
	    //引入库
		vendor('Qiniu.autoload');

		// 构建鉴权对象
		$accessKey = '@';
		$secretKey = '@';
		$auth = new Auth($accessKey, $secretKey);
		
		// 要上传的空间
		$bucket = 'train';
		$bucketMgr = new BucketManager($auth);

		// 上传到七牛后保存的文件名 采用id加当前时间
		$mid = session('is_org') == 1 ? session('member_id') : session('m_id');
		$fileType = pathinfo($key, PATHINFO_EXTENSION);
		//echo $fileType;

		if($fileType == 'mp3'){
		    $return['video'] = $key;
		    $return['status'] = 1;
		}else{
			//转码是使用的队列名称
			$pipeline = 'train';
			$pfop = new PersistentFop($auth, $bucket, $pipeline);

			//要进行转码的转码操作 以便前台直接调iframe观看
			$fops = "avthumb/mp4/s/3840x2160/vb/5m";
			$new_key = $mid.'_'.$key;
			$savekey = Qiniu\base64_urlSafeEncode($bucket.':'.$new_key);
			$fops = $fops.'|saveas/'.$savekey;

			list($id, $err) = $pfop->execute($key, $fops);
			//echo "\n====> pfop avthumb result: \n";
			if ($err != null) {
			    $return = $err;
			} else {
			    $return = $ret;
				if($fileType != 'mp3') $bucketMgr->delete($bucket, $key);
				$return['status'] = 1;
				$return['video'] = $new_key;
			}
		}

		echo json_encode($return);
			

		//查询转码的进度和状态
		//list($ret, $err) = $pfop->status($id);
		////echo "\n====> pfop avthumb status: \n";
		//if ($err != null) {
		//  var_dump($err);
		//} else {
		//  var_dump($ret);
		//}
    }
}
```


```php
//获取token

vendor('Qiniu.autoload');
// 构建鉴权对象
$accessKey = '@';
$secretKey = '@';
$auth = new Auth($accessKey, $secretKey);
// 生成上传 Token
$bucket = 'train';
$this->token = $auth->uploadToken($bucket);
```

相关文档参考：
https://developer.qiniu.com/kodo/sdk/javascript
https://developer.qiniu.com/sdk




