title: jQuery.cropper 插件
date: 2018-07-07
tags: js
toc: true
categories: cropper
---

### 基于 jQuery.cropper 上传可裁剪图片

```
// HTML 部分
<div class="cropper-box">
    <div class="cropper-pos">
        <div class="cropper-container">
            <img src="../img/m/chenrong.jpg" id="image" alt="">
        </div>
        <div class="clearfix control">
            <span class="cancel-file">
            取消上传
        </span>
            <span class="up-file" data-method="getCroppedCanvas">
            上传头像
        </span>
        </div>
    </div>
</div>
```

```
// sass 部分
// 注释：如果插件容器在初始化的时候，容器本身是隐藏的，那会导致无法获取宽度和高度，需要注意，在此我采用透明度和层级来控制的

.cropper-box {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.6);
    z-index: -1;
    opacity: 0;

    .cropper-pos {
        position: absolute;
        top: 40%;
        left: 0;
        transform: translateY(-60%);
        width: 100%;
        height: rem(300);

        .cropper-container {
            width: 100%;
            height: rem(300);
            margin-bottom: rem(40);

            img {
                width: 100%;
            }
        }

        .control {
            display: flex;
            justify-content: space-around;
        }

        .cancel-file,
        .up-file {
            padding: rem(6) rem(14);
            line-height: rem(40);
            text-align: center;
            font-size: rem(22);
            border-radius: rem(4);
            background-color: #eee;
        }

        .up-file {
            background-color: #ffd100;
        }
    }
}
```

```
// JS 部分

// 等比缩放图片
function AutoResizeImage (maxWidth, maxHeight, imgw, imgh) {
    let hRatio
    let wRatio
    let Ratio = 1
    wRatio = maxWidth / imgw
    hRatio = maxHeight / imgh
    if (maxWidth === 0 && maxHeight === 0) {
        Ratio = 1
    } else if (maxWidth === 0) {
        if (hRatio < 1) Ratio = hRatio
    } else if (maxHeight === 0) {
        if (wRatio < 1) Ratio = wRatio
    } else if (wRatio < 1 || hRatio < 1) {
        Ratio = wRatio <= hRatio ? wRatio : hRatio
    }
    if (Ratio < 1) {
        imgw = imgw * Ratio
        imgh = imgh * Ratio
    }
    return {width: imgw, height: imgh}
}

// 图片的 dataurl 转 blob
const dataURLtoBlob = (dataurl) => {
     let arr = dataurl.split(',')
     let mime = arr[0].match(/:(.*?);/)[1]
     let bstr = atob(arr[1])
     let n = bstr.length
     let u8arr = new Uint8Array(n)
     while (n--) {
         u8arr[n] = bstr.charCodeAt(n)
     }
     return new Blob([u8arr], {type: mime})
 }
    
// 使用的是 cropper插件 基于jQuery
// Git 地址 ： https://github.com/fengyuanchen/cropper

// 初始化插件
$('.cropper-container > img').cropper({
        aspectRatio: 1,
        viewMode: 1,
        dragMode: 'move',
        autoCrop: false,
        zoomable: false,
        minCropBoxWidth: 80,
        minCropBoxHeight: 80,
        ready: function () {
            $(this).cropper('crop')
        },
        crop: function (data) {
            $('.cropper-container').css({'width': '100%', 'height': '500px'})
        }
    })

// 上传头像
$(document).on('click', '.up-file', function (e) {
    let formData = new FormData()
    let cropperImg = $('.cropper-container > img').cropper('getCroppedCanvas')
    let dataUrl = cropperImg.toDataURL('image/jpeg')
    let blobUrl = dataURLtoBlob(dataUrl)
    let fileTime = new Date().getTime() + blobUrl.type.split('/')[1]
    let token = Cookie.get('hx_forum_token')
    let passportid = Cookie.get('hx_forum_passportId')

	// 限制上传图片大小
    let imgSize = blobUrl.size / (1024 * 1024)
    if (imgSize > 5) {
	    // 提示语 使用的是 layer 插件
        layer.msg('图片裁剪区域最大不能超过5M！')
        return
    }

    let obj = {
        type: 'user',
        data: JSON.stringify({passportid, token})
    }

    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            formData.append(key, obj[key])
        }
    }

    formData.append('uploadFile', blobUrl, fileTime)
// 上传图片的时候，所有的参数都要变成流传输，所以要借用 FormData 对象
    axiosAjax({
         type: 'POST',
         url: ``,
         contentType: 'multipart/form-data', // *参数格式设置
         params: formData,
         fn: function (data) {
             if (parseInt(data.code) === 1) {
                 $('.portrait-img > img').attr('src', newImg)
             }
         }
     })
    $('.cropper-box').css({'opacity': 0, 'z-index': -1})
})

// 切换图片
$('.portrait-img').on('change', '.file-update', function (e) {
    let width = $('.cropper-container').width()
    let height = $('.cropper-container').height()
    if (window.FileReader) {
        let ReaderObj = new FileReader()
        ReaderObj.readAsDataURL(e.target.files[0])
        ReaderObj.onload = function (res) {
            let imageObj = new Image()
            imageObj.src = res.target.result
            imageObj.onload = function () {
                let proInfo = AutoResizeImage(width, height, imageObj.width, imageObj.height)
                $('#image').cropper('replace', res.target.result, false)
                $('#image').css({'width': proInfo.width, 'height': proInfo.height})
                $('.cropper-box').css({'opacity': 1, 'z-index': 301})
            }
        }
    }
})

```