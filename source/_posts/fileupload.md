---
title: js文件上传的简单方法
date: 2017-11-16 16:24:11
tags: [js, libs, upload]
categories: [收集整理, 摘抄备份]
---

本文是公号开发过程中`ios`，`pc`上图片上传控件的功能，至于`android`是采用的微信`jssdk`中的 `chooseImage`的方法

<!-- more --> 

## upload.js

{% codeblock lang:js %}
export default function upload (options) {
    const {url, file, fileVal, onBeforeSend, onProgress, onError, onSuccess} = options
    const {name, type, lastModifiedDate} = file
    const data = {
        name: name,
        type: type,
        size: options.type == 'file' ? file.size : file.base64.length,
        lastModifiedDate: lastModifiedDate
    }
    const headers = {}

    if (onBeforeSend(file, data, headers) === false) return

    file.status = 'progress'

    onProgress(file, 0)

    const formData = new FormData()
    const xhr = new XMLHttpRequest()

    file.xhr = xhr

    // 设置参数
    Object.keys(data).forEach((key) => {
        formData.append(key, data[key])
    })
    if (options.type == 'file') {
        formData.append(fileVal, file, name)
    } else {
        formData.append(fileVal, file.base64)
    }

    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4) {
            if (xhr.status == 200) {
                try {
                    // 只支持json
                    const ret = JSON.parse(xhr.responseText)
                    onSuccess(file, ret)
                } catch (err) {
                    onError(file, err)
                }
            } else {
                onError(file, new Error('XMLHttpRequest response status is ' + xhr.status))
            }
        }
    }

    // xhr.upload.addEventListener('progress', function (evt) {
    //     if (evt.total == 0) return
    //     const percent = Math.ceil(evt.loaded / evt.total) * 100
    //     onProgress(file, percent)
    // }, false)

    xhr.open('POST', url)

    // 设置头部信息
    Object.keys(headers).forEach((key) => {
        xhr.setRequestHeader(key, headers[key])
    })

    xhr.send(formData)
}
{% endcodeblock %}

## compress.js

{% codeblock lang:js %}
/**
 * 检查图片是否有被压扁，如果有，返回比率
 * ref to http://stackoverflow.com/questions/11929099/html5-canvas-drawimage-ratio-bug-ios
 */
function detectVerticalSquash(img) {
    // 拍照在IOS7或以下的机型会出现照片被压扁的bug
    var data;
    var ih = img.naturalHeight;
    var canvas = document.createElement('canvas');
    canvas.width = 1;
    canvas.height = ih;
    var ctx = canvas.getContext('2d');
    ctx.drawImage(img, 0, 0);
    try {
        data = ctx.getImageData(0, 0, 1, ih).data;
    } catch (err) {
        console.log('Cannot check verticalSquash: CORS?');
        return 1;
    }
    var sy = 0;
    var ey = ih;
    var py = ih;
    while (py > sy) {
        var alpha = data[(py - 1) * 4 + 3];
        if (alpha === 0) {
            ey = py;
        } else {
            sy = py;
        }
        py = (ey + sy) >> 1; // py = parseInt((ey + sy) / 2)
    }
    var ratio = (py / ih);
    return (ratio === 0) ? 1 : ratio;
}

/**
 * dataURI to blob, ref to https://gist.github.com/fupslot/5015897
 * @param dataURI
 */
function dataURItoBuffer(dataURI){
    var byteString = atob(dataURI.split(',')[1]);
    var buffer = new ArrayBuffer(byteString.length);
    var view = new Uint8Array(buffer);
    for (var i = 0; i < byteString.length; i++) {
        view[i] = byteString.charCodeAt(i);
    }
    return buffer;
}
function dataURItoBlob(dataURI) {
    var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];
    var buffer = dataURItoBuffer(dataURI);
    return new Blob([buffer], {type: mimeString});
}

/**
 * 获取图片的orientation
 * ref to http://stackoverflow.com/questions/7584794/accessing-jpeg-exif-rotation-data-in-javascript-on-the-client-side
 */
function getOrientation(buffer){
    var view = new DataView(buffer);
    if (view.getUint16(0, false) != 0xFFD8) return -2;
    var length = view.byteLength, offset = 2;
    while (offset < length) {
        var marker = view.getUint16(offset, false);
        offset += 2;
        if (marker == 0xFFE1) {
            if (view.getUint32(offset += 2, false) != 0x45786966) return -1;
            var little = view.getUint16(offset += 6, false) == 0x4949;
            offset += view.getUint32(offset + 4, little);
            var tags = view.getUint16(offset, little);
            offset += 2;
            for (var i = 0; i < tags; i++)
                if (view.getUint16(offset + (i * 12), little) == 0x0112)
                    return view.getUint16(offset + (i * 12) + 8, little);
        }
        else if ((marker & 0xFF00) != 0xFF00) break;
        else offset += view.getUint16(offset, false);
    }
    return -1;
}

/**
 * 修正拍照时图片的方向
 * ref to http://stackoverflow.com/questions/19463126/how-to-draw-photo-with-correct-orientation-in-canvas-after-capture-photo-by-usin
 */
function orientationHelper(canvas, ctx, orientation) {
    const w = canvas.width, h = canvas.height;
    if(orientation > 4){
        canvas.width = h;
        canvas.height = w;
    }
    switch (orientation) {
        case 2:
            ctx.translate(w, 0);
            ctx.scale(-1, 1);
            break;
        case 3:
            ctx.translate(w, h);
            ctx.rotate(Math.PI);
            break;
        case 4:
            ctx.translate(0, h);
            ctx.scale(1, -1);
            break;
        case 5:
            ctx.rotate(0.5 * Math.PI);
            ctx.scale(1, -1);
            break;
        case 6:
            ctx.rotate(0.5 * Math.PI);
            ctx.translate(0, -h);
            break;
        case 7:
            ctx.rotate(0.5 * Math.PI);
            ctx.translate(w, -h);
            ctx.scale(-1, 1);
            break;
        case 8:
            ctx.rotate(-0.5 * Math.PI);
            ctx.translate(-w, 0);
            break;
    }
}

/**
 * 压缩图片
 */
function compress(file, options, callback) {
    const reader = new FileReader();
    reader.onload = function (evt) {
        if(options.compress === false){
            // 不启用压缩 & base64上传 的分支，不做任何处理，直接返回文件的base64编码
            file.base64 = evt.target.result;
            callback(file);
            return;
        }

        // 启用压缩的分支
        const img = new Image();
        img.onload = function () {
            const ratio = detectVerticalSquash(img);
            const orientation = getOrientation(dataURItoBuffer(img.src));
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');

            const maxW = options.compress.width;
            const maxH = options.compress.height;
            let w = img.width;
            let h = img.height;
            let dataURL;

            if(w < h && h > maxH){
                w = parseInt(maxH * img.width / img.height);
                h = maxH;
            }else if(w >= h && w > maxW){
                h = parseInt(maxW * img.height / img.width);
                w = maxW;
            }

            canvas.width = w;
            canvas.height = h;

            if(orientation > 0){
                orientationHelper(canvas, ctx, orientation);
            }
            ctx.drawImage(img, 0, 0, w, h / ratio);

            if(/image\/jpeg/.test(file.type) || /image\/jpg/.test(file.type)){
                dataURL = canvas.toDataURL('image/jpeg', options.compress.quality);
            }else{
                dataURL =  canvas.toDataURL(file.type);
            }

            if(options.type == 'file'){
                if(/;base64,null/.test(dataURL) || /;base64,$/.test(dataURL)){
                    // 压缩出错，以文件方式上传的，采用原文件上传
                    console.warn('Compress fail, dataURL is ' + dataURL + '. Next will use origin file to upload.');
                    callback(file);
                }else{
                    let blob = dataURItoBlob(dataURL);
                    blob.id = file.id;
                    blob.name = file.name;
                    blob.lastModified = file.lastModified;
                    blob.lastModifiedDate = file.lastModifiedDate;
                    callback(blob);
                }
            }else{
                if(/;base64,null/.test(dataURL) || /;base64,$/.test(dataURL)){
                    // 压缩失败，以base64上传的，直接报错不上传
                    options.onError(file, new Error('Compress fail, dataURL is ' + dataURL + '.'));
                    callback();
                }else{
                    file.base64 = dataURL;
                    callback(file);
                }
            }
        };
        img.src = evt.target.result;
    };
    reader.readAsDataURL(file);
}

export {
    compress
};

{% endcodeblock %}

## index.js

{% codeblock lang:js %}
import { compress } from './compress'
import upload from './upload'

let _id = 0
/**
 * uploader 上传组件
 * @param {string} selector 上传组件的selector
 * @param {object} options 配置项
 * @param {string} [options.url] 上传的url，返回值需要使用json格式
 * @param {boolean} [options.auto=true] 设置为`true`后，不需要手动调用上传，有文件选择即开始上传。用this.upload()来上传，详情请看example
 * @param {string} [options.type=file] 上传类型, `file`为文件上传; `base64`为以base64上传
 * @param {string=} [options.fileVal=file] 文件上传域的name
 * @param {object=} [options.compress] 压缩配置, `false`则不压缩
 * @param {number=} [options.compress.width=1600] 图片的最大宽度
 * @param {number=} [options.compress.height=1600] 图片的最大高度
 * @param {number=} [options.compress.quality=.8] 压缩质量, 取值范围 0 ~ 1
 * @param {function=} [options.onBeforeQueued] 文件添加前的回调，return false则不添加
 * @param {function=} [options.onQueued] 文件添加成功的回调
 * @param {function=} [options.onBeforeSend] 文件上传前调用，具体参数看example
 * @param {function=} [options.onSuccess] 上传成功的回调
 * @param {function=} [options.onProgress] 上传进度的回调
 * @param {function=} [options.onError] 上传失败的回调
 *
 * @example
 * var uploadCount = 0;
 * weui.uploader('#uploader', {
 *    url: 'http://localhost:8081',
 *    auto: true,
 *    type: 'file',
 *    fileVal: 'fileVal',
 *    compress: {
 *        width: 1600,
 *        height: 1600,
 *        quality: .8
 *    },
 *    onBeforeQueued: function(files) {
 *        // `this` 是轮询到的文件, `files` 是所有文件
 *
 *        if(["image/jpg", "image/jpeg", "image/png", "image/gif"].indexOf(this.type) < 0){
 *            weui.alert('请上传图片');
 *            return false; // 阻止文件添加
 *        }
 *        if(this.size > 10 * 1024 * 1024){
 *            weui.alert('请上传不超过10M的图片');
 *            return false;
 *        }
 *        if (files.length > 5) { // 防止一下子选择过多文件
 *            weui.alert('最多只能上传5张图片，请重新选择');
 *            return false;
 *        }
 *        if (uploadCount + 1 > 5) {
 *            weui.alert('最多只能上传5张图片');
 *            return false;
 *        }
 *
 *        ++uploadCount;
 *
 *        // return true; // 阻止默认行为，不插入预览图的框架
 *    },
 *    onQueued: function(){
 *        console.log(this);
 *
 *        // console.log(this.status); // 文件的状态：'ready', 'progress', 'success', 'fail'
 *        // console.log(this.base64); // 如果是base64上传，file.base64可以获得文件的base64
 *
 *        // this.upload(); // 如果是手动上传，这里可以通过调用upload来实现；也可以用它来实现重传。
 *        // this.stop(); // 中断上传
 *
 *        // return true; // 阻止默认行为，不显示预览图的图像
 *    },
 *    onBeforeSend: function(data, headers){
 *        console.log(this, data, headers);
 *        // $.extend(data, { test: 1 }); // 可以扩展此对象来控制上传参数
 *        // $.extend(headers, { Origin: 'http://127.0.0.1' }); // 可以扩展此对象来控制上传头部
 *
 *        // return false; // 阻止文件上传
 *    },
 *    onProgress: function(procent){
 *        console.log(this, procent);
 *        // return true; // 阻止默认行为，不使用默认的进度显示
 *    },
 *    onSuccess: function (ret) {
 *        console.log(this, ret);
 *        // return true; // 阻止默认行为，不使用默认的成功态
 *    },
 *    onError: function(err){
 *        console.log(this, err);
 *        // return true; // 阻止默认行为，不使用默认的失败态
 *    }
 * });
 */
function hasClass (obj, cls) {
  return obj.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'))
}

function addClass (obj, cls) {
  if (!hasClass(obj, cls)) obj.className += ' ' + cls
}

function removeClass (obj, cls) {
  if (hasClass(obj, cls)) {
    let reg = new RegExp('(\\s|^)' + cls + '(\\s|$)')
    obj.className = obj.className.replace(reg, ' ')
  }
}

function uploader (selector, options) {
    const $uploader = document.querySelector(selector)
    const URL = window.URL || window.webkitURL || window.mozURL

    // 找到DOM里file-content，若无，则插入一个。
    function findFileCtn ($uploader, id) {
        const $file = $uploader.querySelector('[data-id="' + id + '"]')
        if (!$file) return null

        let $fileCtn = $file.querySelector('.weui-uploader__file-content')
        if (!$fileCtn) {
            $fileCtn = document.createElement('div')
            $fileCtn.setAttribute('class', 'weui-uploader__file-content')
            $file.appendChild($fileCtn)
        }
        addClass($file, 'weui-uploader__file_status')
        return $fileCtn
    }

    // 设置上传
    function setUploadFile (file) {
        file.url = URL.createObjectURL(file)
        file.status = 'ready'
        file.upload = function () {
            upload(Object.assign({
                file: file
            }, options))
        }
        file.stop = function () {
            this.xhr.abort()
        }
        options.onQueued(file)
        if (options.auto) file.upload()
    }
    let noop = function () { }
    options = Object.assign({
        url: '',
        auto: true,
        type: 'file',
        fileVal: 'file',
        onBeforeQueued: noop,
        onQueued: noop,
        onBeforeSend: noop,
        onSuccess: noop,
        onProgress: noop,
        onError: noop
    }, options)

    if (options.compress !== false) {
        options.compress = Object.assign({
            width: 1600,
            height: 1600,
            quality: 0.8
        }, options.compress)
    }

    if (options.onBeforeQueued) {
        const onBeforeQueued = options.onBeforeQueued
        options.onBeforeQueued = function (file, files) {
            const ret = onBeforeQueued.call(file, files)
            if (ret === false) {
                return false
            }
            if (ret === true) {
                return
            }
        }
    }
    if (options.onQueued) {
        const onQueued = options.onQueued
        options.onQueued = function (file) {
            onQueued.call(file, file)
        }
    }
    if (options.onBeforeSend) {
        const onBeforeSend = options.onBeforeSend
        options.onBeforeSend = function (file, data, headers) {
            const ret = onBeforeSend.call(file, data, headers)
            if (ret === false) {
                return false
            }
        }
    }
    if (options.onSuccess) {
        const onSuccess = options.onSuccess
        options.onSuccess = function (file, ret) {
            file.status = 'success'
            onSuccess.call(file, ret, file)
        }
    }
    if (options.onProgress) {
        const onProgress = options.onProgress
        options.onProgress = function (file, percent) {
            if (!onProgress.call(file, percent)) {
                let span = findFileCtn($uploader, file.id)
                span && (span.innerHTML = percent + '%')
            }
        }
    }
    if (options.onError) {
        const onError = options.onError
        options.onError = function (file, err) {
            file.status = 'fail'
            onError.call(file, err)
        }
    }

    $uploader.querySelector('input[type="file"]').addEventListener('change', function (evt) {
        const files = evt.target.files
        if (files.length === 0) { return }

        if (options.compress === false && options.type == 'file') {
            // 以原文件方式上传
            Array.prototype.forEach.call(files, (file) => {
                file.id = ++_id
                if (options.onBeforeQueued(file, files) === false) return
                setUploadFile(file)
            })
        } else {
            // base64上传 和 压缩上传
            Array.prototype.forEach.call(files, (file) => {
                file.id = ++_id
                if (options.onBeforeQueued(file, files) === false) return
                compress(file, options, function (blob) {
                    if (blob) setUploadFile(blob)
                })
            })
        }
        this.value = ''
    })
}
export default uploader

{% endcodeblock %}

## 调用方法

{% codeblock lang:js %}
import uploader from './index'
uploader('#upload-file', {
    url: '/system/file/upload',
    auto: true, //自动上传
    type: 'file',
    compress: {
        width: 1600,
        height: 1600,
        quality: 0.8
    },
    onBeforeQueued: function (files) {
        let text = ''
        if (['image/jpg', 'image/jpeg', 'image/png', 'image/gif'].indexOf(this.type) < 0) {
            text = '请上传图片'
        }
        if (this.size > 10 * 1024 * 1024) {
            text = '请上传不超过10M的图片'
        }
        if (files.length > 9) {
            text = '每次最多只能上传9张图片，请重新选择'
        }
        if (text) {
            return alert(text), false;
        }
    },
    onQueued: function (file) {
        // that.onQueued(file)
    },
    onSuccess: function (res, file) {
        // res 后台回传参数
        /*that.onSuccess({
            id: file.id,
            _id: res.upid,
            _url: res.urlpath
        })*/
    },
    onError: function (file, err) {
        //that.onError(file.id)
    }
})
{% endcodeblock %}

## 总结

备注下上传方法，其中包括了图片压缩，校正，转流等方法