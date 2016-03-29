---
layout: post
title: 微信企业支付接口
categories: wechat
---

<div class="data-box with-padding">
      <div class="data-hd">
       <h3>接口地址 </h3>
      </div>
      <div class="data-bd">
       <p  class="mb20">接口链接：https://api.mch.weixin.qq.com/mmpaymkttransfers/promotion/transfers</p>
      </div>
    </div>
	<div class="data-box with-padding">
      <div class="data-hd">           
       <h3>是否需要证书 </h3>
      </div>
      <div class="data-bd">           
	   <p>请求需要双向证书。 详见<a  target="_blank"  href="?chapter=4_3">证书使用</a></p>
      </div>
    </div>   
    <div class="data-box with-padding">
        <div class="data-hd">      
    <h3>请求参数</h3>
        </div>
        <div class="data-bd">
    <div  class="table-wrp with-border">
      <table  class="table">
        <thead>
      	<tr>
			<th class="name">字段名</th>
			<th class="var">变量名</th>
			<th class="require">必填</th>
			<th class="type">示例值</th>
			<th class="example">类型</th>
		   <th class="description">描述</th>
      	</tr>
      	</thead>
        <tr>
          <td>公众账号appid</td>
          <td>mch_appid</td>
          <td>是</td>
          <td>wx8888888888888888</td>
          <td>String</td>
          <td>微信分配的公众账号ID（企业号corpid即为此appId）</td>
        </tr>
        <tr>
          <td>商户号</td>
          <td>mchid</td>
          <td>是</td>
          <td>1900000109</td>
          <td>String(32)</td>
          <td>微信支付分配的商户号</td>
        </tr>
        <tr>
          <td>设备号</td>
          <td>device_info</td>
          <td>否</td>
          <td>013467007045764</td>
          <td>String(32)</td>
          <td>微信支付分配的终端设备号</td>
        </tr>
        <tr>
          <td>随机字符串</td>
          <td>nonce_str</td>
          <td>是</td>
          <td>5K8264ILTKCH16CQ2502SI8ZNMTM67VS</td>
          <td>String(32)</td>
          <td>随机字符串，不长于32位</td>
        </tr>
        <tr>
          <td>签名</td>
          <td>sign</td>
          <td>是</td>
          <td>C380BEC2BFD727A4B6845133519F3AD6</td>
          <td>String(32)</td>
          <td >签名，详见<a  target="_blank"  href="?chapter=4_3" >签名算法</a></td>
        </tr>
        <tr>
          <td>商户订单号</td>
          <td>partner_trade_no</td>
          <td>是</td>
          <td>10000098201411111234567890</td>
          <td>String</td>
          <td>商户订单号，需保持唯一性</td>
        </tr>
        <tr>
          <td>用户openid</td>
          <td>openid</td>
          <td>是</td>
          <td>oxTWIuGaIt6gTKsQRLau2M0yL16E</td>
          <td>String</td>
          <td>商户appid下，某用户的openid</td>
        </tr>
        <tr>
          <td>校验用户姓名选项</td>
          <td>check_name</td>
          <td>是</td>
          <td>OPTION_CHECK</td>
          <td>String</td>
          <td>NO_CHECK：不校验真实姓名 <br />
              FORCE_CHECK：强校验真实姓名（未实名认证的用户会校验失败，无法转账） <br />
              OPTION_CHECK：针对已实名认证的用户才校验真实姓名（未实名认证用户不校验，可以转账成功）</td>
        </tr>
        <tr>
          <td>收款用户姓名</td>
          <td>re_user_name</td>
          <td>可选</td>
          <td>马花花</td>
          <td>String</td>
          <td>收款用户真实姓名。 <br />
              如果check_name设置为FORCE_CHECK或OPTION_CHECK，则必填用户真实姓名</td>
        </tr>
        <tr>
          <td>金额</td>
          <td>amount</td>
          <td>是</td>
          <td>10099</td>
          <td>int</td>
          <td>企业付款金额，单位为分</td>
        </tr>
        <tr>
          <td>企业付款描述信息</td>
          <td>desc</td>
          <td>是</td>
          <td>理赔</td>
          <td>String</td>
          <td>企业付款操作说明信息。必填。</td>
        </tr>
        <tr>
          <td>Ip地址</td>
          <td>spbill_create_ip</td>
          <td>是</td>
          <td>192.168.0.1</td>
          <td>String(32)</td>
          <td>调用接口的机器Ip地址</td>
        </tr>
      </table>
    </div>
    <p class="mb10">数据示例： </p>
    <div class="guide-msg mb10">
        <p >&lt;xml&gt; </p>
        <p >&lt;mch_appid&gt;wxe062425f740c30d8&lt;/mch_appid&gt; </p>
        <p >&lt;mchid&gt;10000098&lt;/mchid&gt; </p>
        <p >&lt;nonce_str&gt;3PG2J4ILTKCH16CQ2502SI8ZNMTM67VS&lt;/nonce_str&gt; </p>
        <p >&lt;partner_trade_no&gt;100000982014120919616&lt;/partner_trade_no&gt; </p>
        <p >&lt;openid&gt;ohO4Gt7wVPxIT1A9GjFaMYMiZY1s&lt;/openid&gt; </p>
        <p >&lt;check_name&gt;OPTION_CHECK&lt;/check_name&gt; </p>
        <p >&lt;re_user_name&gt;张三&lt;/re_user_name&gt; </p>
        <p >&lt;amount&gt;100&lt;/amount&gt; </p>
        <p >&lt;desc&gt;节日快乐!&lt;/desc&gt; </p>
        <p >&lt;spbill_create_ip&gt;10.2.3.10&lt;/spbill_create_ip&gt; </p>
        <p >&lt;sign&gt;C97BDBACF37622775366F38B629F45E3&lt;/sign&gt; </p>
        <p >&lt;/xml&gt;</p>
    </div>
        </div>
    </div>    
    <div class="data-box with-padding">
        <div class="data-hd">      
    <h3>返回参数</h3>
        </div>
        <div class="data-bd">
    <div class="table-wrp with-border">
      <table class="table">
        <thead>
      	<tr>
			<th class="name">字段名</th>
			<th class="var">变量名</th>
			<th class="require">必填</th>
			<th class="type">示例值</th>
			<th class="example">类型</th>
		   <th class="description">描述</th>
      	</tr>
      	</thead>
        <tr>
          <td>返回状态码</td>
          <td>return_code</td>
          <td>是</td>
          <td>SUCCESS</td>
          <td>String(16)</td>
          <td>SUCCESS/FAIL<br />
              此字段是通信标识，非交易标识，交易是否成功需要查看result_code来判断</td>
        </tr>
        <tr>
          <td>返回信息</td>
          <td>return_msg</td>
          <td>否</td>
          <td>签名失败</td>
          <td>String(128)</td>
          <td>返回信息，如非空，为错误原因 <br />
              签名失败 <br />
              参数格式校验错误</td>
        </tr>
		</table>
       </div>
        <p class="mb10">以下字段在return_code为SUCCESS的时候有返回 </p>
		<div class="table-wrp with-border">
		<table  class="table">
		<thead>
      	<tr>
			<th class="name">字段名</th>
			<th class="var">变量名</th>
			<th class="require">必填</th>
			<th class="type">示例值</th>
			<th class="example">类型</th>
		   <th class="description">描述</th>
      	</tr>
      	</thead>
        <tr>
          <td>商户appid</td>
          <td>mch_appid</td>
          <td>是</td>
          <td>wx8888888888888888</td>
          <td>String</td>
          <td>微信分配的公众账号ID（企业号corpid即为此appId）</td>
        </tr>
        <tr>
          <td>商户号</td>
          <td>mchid</td>
          <td>是</td>
          <td>1900000109</td>
          <td>String(32)</td>
          <td>微信支付分配的商户号</td>
        </tr>
        <tr>
          <td>设备号</td>
          <td>device_info</td>
          <td>否</td>
          <td>013467007045764</td>
          <td>String(32)</td>
          <td>微信支付分配的终端设备号，</td>
        </tr>
        <tr>
          <td>随机字符串</td>
          <td>nonce_str</td>
          <td>是</td>
          <td>5K8264ILTKCH16CQ2502SI8ZNMTM67VS</td>
          <td>String(32)</td>
          <td>随机字符串，不长于32位</td>
        </tr>
        <tr>
          <td>业务结果</td>
          <td>result_code</td>
          <td>是</td>
          <td>SUCCESS</td>
          <td>String(16)</td>
          <td>SUCCESS/FAIL</td>
        </tr>
        <tr>
          <td>错误代码</td>
          <td>err_code</td>
          <td>否</td>
          <td>SYSTEMERROR</td>
          <td>String(32)</td>
          <td>错误码信息</td>
        </tr>
        <tr>
          <td>错误代码描述</td>
          <td>err_code_des</td>
          <td>否</td>
          <td>系统错误 </td>
          <td>String(128)</td>
          <td>结果信息描述</td>
        </tr>
		</table>
       </div>
         <p class="mb10">以下字段在return_code&nbsp;和result_code都为SUCCESS的时候有返回 </p>
		 <div class="table-wrp with-border">
      <table class="table">
	  <thead>
      <tr>
        <th class="name">字段名</th>
        <th class="var">变量名</th>
        <th class="require">必填</th>
        <th class="type">示例值</th>
        <th class="example">类型</th>
       <th class="description">描述</th>
      </tr>
        <tr>
          <td>商户订单号</td>
          <td>partner_trade_no</td>
          <td>是</td>
          <td>1217752501201407033233368018</td>
          <td>String(32)</td>
          <td>商户订单号，需保持唯一性</td>
        </tr>
        <tr>
          <td>微信订单号</td>
          <td>payment_no</td>
          <td>是</td>
          <td>1007752501201407033233368018 </td>
          <td>String</td>
          <td>企业付款成功，返回的微信订单号</td>
        </tr>
        <tr>
          <td>微信支付成功时间</td>
          <td>payment_time</td>
          <td>是</td>
          <td>2015-05-19 15：26：59</td>
          <td>String</td>
          <td>企业付款成功时间</td>
        </tr>
      </table>
    </div>
    <p class="mb10">成功示例： </p>
    <div class="guide-msg mb10">
          <p >&lt;xml&gt; </p>
          <p >&#9;&lt;return_code&gt;&lt;![CDATA[SUCCESS]]&gt;&lt;/return_code&gt; </p>
          <p >&#9;&lt;return_msg&gt;&lt;![CDATA[]]&gt;&lt;/return_msg&gt; </p>
          <p >&#9;&lt;mch_appid&gt;&lt;![CDATA[wxec38b8ff840bd989]]&gt;&lt;/mch_appid&gt; </p>
          <p >&#9;&lt;mchid&gt;&lt;![CDATA[10013274]]&gt;&lt;/mchid&gt; </p>
          <p >&#9;&lt;device_info&gt;&lt;![CDATA[]]&gt;&lt;/device_info&gt; </p>
          <p >&#9;&lt;nonce_str&gt;&lt;![CDATA[lxuDzMnRjpcXzxLx0q]]&gt;&lt;/nonce_str&gt; </p>
          <p >&#9;&lt;result_code&gt;&lt;![CDATA[SUCCESS]]&gt;&lt;/result_code&gt; </p>
          <p >&#9;&lt;partner_trade_no&gt;&lt;![CDATA[10013574201505191526582441]]&gt;&lt;/partner_trade_no&gt; </p>
          <p >&#9;&lt;payment_no&gt;&lt;![CDATA[1000018301201505190181489473]]&gt;&lt;/payment_no&gt; </p>
          <p >&#9;&lt;payment_time&gt;&lt;![CDATA[2015-05-19&nbsp;15：26：59]]&gt;&lt;/payment_time&gt; </p>
          <p >&lt;/xml&gt;</p>
     </div>
    <p  class="mb10">错误示例： </p>
    <div class="guide-msg mb10">
        <p>&lt;xml&gt; </p>
        <p >&#9;&lt;return_code&gt;&lt;![CDATA[FAIL]]&gt;&lt;/return_code&gt; </p>
        <p >&#9;&lt;return_msg&gt;&lt;![CDATA[系统繁忙,请稍后再试.]]&gt;&lt;/return_msg&gt; </p>
        <p >&#9;&lt;result_code&gt;&lt;![CDATA[FAIL]]&gt;&lt;/result_code&gt; </p>
        <p >&#9;&lt;err_code&gt;&lt;![CDATA[SYSTEMERROR]]&gt;&lt;/err_code&gt; </p>
        <p >&#9;&lt;err_code_des&gt;&lt;![CDATA[系统繁忙,请稍后再试.]]&gt;&lt;/err_code_des&gt; </p>
        <p >&lt;/xml&gt;</p>
    </div>
        </div>
    </div>    
    <div class="data-box with-padding">
        <div class="data-hd">      
	<h3>错误码</h3>
        </div>
        <div class="data-bd">
    <div  class="table-wrp with-border">
      <table class="table">
      <tr>
        <th>错误代码</th>
        <th>描述</th>
        <th>原因</th>
        <th>解决方案</th>
      </tr>
      <tr>
        <td>NOAUTH</td>
        <td>没有权限</td>
        <td>没有授权请求此api</td>
        <td>请联系微信支付开通api权限</td>
      </tr>
	  <tr>
        <td>AMOUNT_LIMIT</td>
        <td>付款金额不能小于最低限额</td>
        <td>付款金额不能小于最低限额</td>
        <td>每次付款金额必须大于1元</td>
      </tr>
      <tr>
        <td>PARAM_ERROR</td>
        <td>参数错误</td>
        <td>参数缺失，或参数格式出错，参数不合法等</td>
        <td>请查看err_code_des，修改设置错误的参数</td>
      </tr>
      <tr>
        <td>OPENID_ERROR</td>
        <td>Openid错误</td>
        <td>Openid格式错误或者不属于商家公众账号</td>
        <td>请核对商户自身公众号appid和用户在此公众号下的openid。</td>
      </tr>
      <tr>
        <td>NOTENOUGH</td>
        <td>余额不足</td>
        <td>帐号余额不足</td>
        <td>请用户充值或更换支付卡后再支付</td>
      </tr>
      <tr>
        <td>SYSTEMERROR</td>
        <td>系统繁忙，请稍后再试。</td>
        <td>系统错误，请重试</td>
        <td>使用原单号以及原请求参数重试</td>
      </tr>
      <tr>
        <td>NAME_MISMATCH</td>
        <td>姓名校验出错</td>
        <td>请求参数里填写了需要检验姓名，但是输入了错误的姓名</td>
        <td>填写正确的用户姓名</td>
      </tr>
      <tr>
        <td>SIGN_ERROR</td>
        <td>签名错误</td>
        <td>没有按照文档要求进行签名</td>
        <td>
        <ol>
          <li>签名前没有按照要求进行排序。 </li>
          <li>没有使用商户平台设置的密钥进行签名 </li>
          <li>参数有空格或者进行了encode后进行签名。 </li>
        </ol>
        </td>
      </tr>
      <tr>
        <td>XML_ERROR</td>
        <td>Post内容出错</td>
        <td>Post请求数据不是合法的xml格式内容</td>
        <td>修改post的内容</td>
      </tr>
      <tr>
        <td>FATAL_ERROR</td>
        <td>两次请求参数不一致</td>
        <td>两次请求商户单号一样，但是参数不一致</td>
        <td>如果想重试前一次的请求，请用原参数重试，如果重新发送，请更换单号。</td>
      </tr>
      <tr>
        <td>CA_ERROR</td>
        <td>证书出错</td>
        <td>请求没带证书或者带上了错误的证书</td>
        <td>
        <ol>
          <li>到商户平台下载证书 </li>
          <li>请求的时候带上该证书 </li>
        </ol>
        </td>
      </tr>
    </table>
	</div>
  </div>
  </div>
</div>
