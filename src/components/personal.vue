<template>
	<div>
		<header>
		   <span>{{this.$store.state.UserName}}</span><i class="edit icon" @click="slideInfoBox"></i>
		</header>
		<div class="info-box">
			<div class="op-box"> 
				<div class="operate-box">
					<div class="op-icon add-account"></div>
					<button class="" @click="changeAccount">填写支付帐号</button>
				</div>
				<div class="operate-box">
			      	<div class="op-icon img">
			      		<img :src="src"/>
			      	</div>
			      	<label><input type="file" id="portrait" name="portrait" @change="changePortrait">更换头像</label>
			    </div> 
				<div class="operate-box">
					<div class="op-icon chang-pwd"></div>
					<button class="" @click="changePwd">修改密码</button>
				</div>
			</div>
			<div class="op-content" v-if="isChangeAccount">
			<form class="form-horizontal form">
				  <div class="form-group">
				    <label for="inputRpwd" class="col-sm-4 control-label">支付帐号：</label>
				    <div class="col-sm-4">
				      <input type="text" class="form-control" name="inputAccount"
				      v-model.trim='inputAccount' placeholder='支付帐号'>
				    </div>
				  </div>
				  <div class="form-group btn-box">
				    <div class="col-sm-offset-2 col-sm-8">
				      <button type="button" class="btn btn-cancel" @click='cancel'>取消
					  </button>
					  <span></span>
					  <button type="button" class="btn btn-submit" @click="insertAccount">提交</button>
				    </div>
				  </div>
				  </form>
			</div>
			<div class="op-content" v-if="isChangeImg">
				<img :src="newSrc" ><br/>
					<div class="btn-box">
						<button type="button" class="btn btn-cancel" @click='cancel'>取消
					  </button>
					  <span></span>
					  <button type="button" class="btn btn-submit" @click="modifyImg">提交</button>
					</div>
			</div>
			<div class="op-content" v-if="isChangePwd">
				<form class="form-horizontal form">
				  <div class="form-group">
				    <label for="inputPwd" class="col-sm-4 control-label">密&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;码：</label>
				    <div class="col-sm-4">
				      <input type="password" class="form-control" id="inputEmail"
				      name="pwd" v-model.trim='pwd' placeholder="密码">
				    </div>
				  </div>
				  <div class="form-group">
				    <label for="inputRpwd" class="col-sm-4 control-label">重复密码：</label>
				    <div class="col-sm-4">
				      <input type="password" class="form-control" id="inputRpwd"
				      v-model.trim='rpwd' placeholder="重复密码">
				    </div>
				  </div>
				  <div class="form-group btn-box">
				    <div class="col-sm-offset-2 col-sm-8">
				      <button type="button" class="btn btn-cancel" @click='cancel'>取消
					  </button>
					  <span></span>
					  <button type="button" class="btn btn-submit" @click="modifyPwd">提交</button>
				    </div>
				  </div>
				  </form>
			</div>
		</div>
		<div id="count-charts"></div>
		<div id="money-charts"></div>
	</div>
	
</template>
<script>
import echarts from 'echarts/dist/echarts.min.js';
export default {
  data () {
    return {
    	newSrc: '',   //新头像图片src
    	src: this.$store.state.UserImg,      //上传成功后更新头像src
    	newImg: '',   //上传的图片
    	isChangeImg: false,
    	isChangePwd: false,
    	isChangeAccount: false,
    	inputAccount: null,
    	pwd: '',
    	rpwd: '',
    	// echarts图表配置
    	countOption: {
    		title : {
		        text: '各类商品占比',
		        x:'center',
		        textStyle: {
                    fontWeight: 'normal',              //标题颜色
                    color: '#000'
                }
		    },
		    tooltip : {
		        trigger: 'item',
		        formatter: "{a} <br/>{b} : {c} ({d}%)"
		    },
		    backgroundColor: 'rgba(255, 255, 255, .3)',
		    legend: {
		        orient: 'vertical',
		        left: 'left',
		        textStyle: {
                    color: '#000'
                },
		        data: ['发布商品','收藏商品','买入商品','售出商品']
		    },
		    series : [
		        {
		            name: '各类商品占比',
		            type: 'pie',
		            radius : '70%',
		            center: ['50%', '55%'],
		            data:[
		                {value:0, name:'发布商品'},
		                {value:0, name:'收藏商品'},
		                {value:0, name:'买入商品'},
		                {value:0, name:'售出商品'}
		            ],
		            itemStyle: {
		                emphasis: {
		                    shadowBlur: 10,
		                    shadowOffsetX: 0,
		                    shadowColor: 'rgba(0, 0, 0, 0.5)'
		                }
		            }
		        }
		    ]
    	},
    	moneyOption: {
    		title : {
		        text: '收支情况',
		        x:'center',
		        textStyle: {
                    fontWeight: 'normal',              //标题颜色
                    color: '#000'
                },
		    },
		    color:['#333366','#CC0033'],  
		    backgroundColor: 'rgba(255, 255, 255, .3)',
		    tooltip : {
		        trigger: 'item',
		        formatter: "{a} <br/>{b} : ￥{c} ({d}%)"
		    },
		    legend: {
		        orient: 'vertical',
		        left: 'left',
		        textStyle: {
                    color: '#000'
                },
		        data: ['收入','支出']
		    },
    		calculable : true,
		    series : [
		        {
		            name: '收支情况',
		            type: 'pie',
		            radius : ['50%', '70%'],
		            center: ['50%', '55%'],
		            data:[],
		            itemStyle: {
		                emphasis: {
		                    shadowBlur: 10,
		                    shadowOffsetX: 0,
		                    shadowColor: 'rgba(0, 0, 0, 0.5)'
		                }
		            }
		        }
		    ]
    	}
    }
  },
  mounted: function() {
  	var myChart1 = echarts.init(document.getElementById('count-charts'));
  	var myChart2 = echarts.init(document.getElementById('money-charts'));
  	var _this = this;
  	$.ajax({
	    url: "/api/user/echartsInit",
	    type: "get",
        beforeSend: function(xhr) {
          _this.myFun.setToken(xhr);
        },
	    success: function(data){
	    	console.log(data)
	    	var countOption = _this.countOption.series[0].data;
	    	for (var i = 0; i < countOption.length; i++) {
	    		countOption[i].value = data.countOption[i];
	    	}
	    	_this.moneyOption.series[0].data = data.moneyOption;
  			myChart1.setOption(_this.countOption);
  			myChart2.setOption(_this.moneyOption);
  			window.onresize = function() {
  				myChart1.resize();
  				myChart2.resize();
  			}
	    },
	    error: function(err) {
	    	console.log('获取资源失败')
            _this.myFun.tokenExpired(err)
	    }
	});
	setTimeout(() => {
		window.onresize = function() {
			myChart.resize();
		}
	}, 20)   
  },
  methods: {
  	slideInfoBox() {
  		$('.info-box').slideToggle();
		setTimeout(()=> {
			this.isChangeImg = false;
			this.isChangePwd = false;
			this.isChangeAccount = false;
		}, 1000)
  	},
  	changePortrait(e) {
  		this.isChangeImg = true;
  		this.isChangePwd = false;
    	this.isChangeAccount = false;
  		var files = e.target.files || e.dataTransfer.files;
  		this.newImg = files[0];
  		if(typeof FileReader==='undefined'){
            alert('您的浏览器不支持图片上传，请升级您的浏览器');
            return false;
        }
        var vm = this;
        var leng = files.length;
        for(var i = 0; i < leng; i++){
            var reader = new FileReader();
            reader.readAsDataURL(files[i]); 
            reader.onload = function(e) {
            	vm.newSrc = e.target.result;
            };                 
        }
  	},
  	changePwd() {
  		this.isChangePwd = true;
  		this.isChangeImg = false;
    	this.isChangeAccount = false;
  	},
  	modifyPwd() {
  		if(this.pwd != this.rpwd) {
  			this.myFun.showMsg('两次密码不一致', 0);
  		} else if(this.pwd == '' || this.rpwd == '') {
  			this.myFun.showMsg('密码不能为空', 0);
  		} else {
  			this.$http.post('/api/user/modifyInfo', {
  				newPwd: this.rpwd,
  				token: this.$store.state.Token
		    },{}).then((response) => {
		    	if(response.code = 200){
			    	this.myFun.showMsg('密码修改成功', 1)
					this.isChangePwd = false;
				}
		    })
		    .catch(function(response) {
		        console.log("出现异常");
		    })
  		}
  	},
  	modifyImg() {
  		var formdata = new FormData();  		
		formdata.enctype = "multipart/form-data";
		formdata.append('newImg', this.newImg);
		formdata.append('token', this.$store.state.Token)
		var _this = this;
		$.ajax({
            url: "/api/user/modifyInfo",
            type: "post",
            data: formdata,
            processData: false, // 不处理数据
            contentType: false, // 不设置请求头
            cache: false,
            success: function(data){
                if(data.imgSrc) {
                	_this.myFun.showMsg('上传头像成功', 1)
                	_this.src = _this.newSrc;
                	_this.$store.commit('setUserImg', data.imgSrc);
                	$('#nav_portrait').attr("src",data.imgSrc);
                	_this.isChangeImg = false;
                }
                
            },
            error: function(error) {
            	_this.myFun.showMsg("上传头像失败", 0);
            }
        })
  	},
  	changeAccount () {
  		this.isChangeAccount = true;
  		this.isChangePwd = false;
  		this.isChangeImg = false;
  	},
  	insertAccount() {
  		console.log(this.inputAccount)
  		this.$http.post('/api/user/insertAccount', {
				account: this.inputAccount
		    },{}).then((response) => {
		    	this.inputAccount = null;
				this.myFun.showMsg('填写支付帐号成功', 1)
				this.isChangeAccount = false;
		    })
		    .catch(function(response) {
		        console.log("异常");
		    })
  	},
  	cancel() {
  		this.isChangePwd = false;
  		this.isChangeImg = false;
  		this.isChangeAccount = false;
  	}
  }
}
</script>
<style lang="less"> 
header {
	width: 100%;
	height: 30px;
	margin-bottom: 30px;
	color: #eee;
	span {
	  	display: inline-block;
	    position: relative;
	    bottom: 7px;
	    margin-left: 10px;
	    font-size: 20px;
	}
}
.info-box{
	display: none;
}
@keyframes show-box {
	to {
		transform: translateX(0px);
		opacity: 1;
	}
}
.op-box {
	width: 96%;
	height: 180px;
	margin: 0 auto;
	margin-bottom: 1em;
	background: rgba(255, 255, 255, 0.9);
	display: flex;
	justify-content: center;
	.operate-box {
	   width: 8.7rem;
	   height: 7.5rem;
	   border: 1px solid #ccc;
	   margin: 15px;
	   transform: translateX(-200px);
	   opacity: 0;
	   animation: show-box 1s;
       animation-fill-mode: forwards;
	   .img {
	   	width: 100%;
	   	height: 7.5rem;
	   }
	   img {
	     max-width: 100%;
	     max-height: 100%;
	     &:hover{
	       cursor: pointer;
	     }
	  }
	  label, button {
	  	display: block;
	  	width: 8.7rem;
	  	line-height: 30px;
		color: #fff;
		font-weight: normal;
	  	background: rgba(240, 27, 45, 0.9);
	  	&:hover {
	  		cursor: pointer;
	  		background: rgb(240, 27, 45);
	  	}
	  	input {
	  		display: none;
	  	}
	  }
	  button {
	  	margin-top: -1px;
	  }
	}
}
.chang-pwd {
	height: 7.5rem;
	background-image: url(../assets/img/key.png);
	background-repeat: no-repeat;
	background-position: center center;
}
.add-account {
	height: 7.5rem;
	background-image: url(../assets/img/money.png);
	background-repeat: no-repeat;
	background-position: center center;	
}
.op-content {
	margin-bottom: 2rem;
	width: 100%;
	min-height: 16rem;
	padding: 1rem 0;
	background: #eee;
	img {
		width: 250px;
		max-height: 100%;
	}
}
#count-charts, #money-charts {
	display: inline-block;
	width: 48%;
	height: 25rem;
	margin: 0 auto;
	border-radius: 10px;
	padding: 10px;
}
@media screen and (max-width: 600px) {
	.op-box {
		height: 80px;
		width: 100%;
		.operate-box {
			height: 0;
		}
		.op-icon {
			display: none;
		}
	}
	#count-charts, #money-charts {
		width: 100%;
		margin-bottom: 1rem;
	}
}
</style>