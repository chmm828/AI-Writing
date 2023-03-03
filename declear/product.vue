<template>
    <div class="wrapper-box">
        <PageHeader bgcolor="#4A25BA" color="white"/>
        <div class="content">
            <h1>产品声明</h1>
            <p>本网站提供搜索服务的数据来源于互联网中公开信息，本站只提供公开信息检索和分析服务。素材内容的版权归原作者所有，本站不对齐其观点和对其真实性负责，也不构成任何其他建议。请写作者根据需要酌情使用。</p>
            <p>Get智能写作是一款基于人工智能技术的人机协作的写作工具，人工智能技术仅提供写作辅助，写作内容依旧由用户主导。最终成文结果由写作者自行把控。</p>
            <p>如写作者在文章中引用他人内容，需要尊重原作者或素材提供者的版权权力。我们支持原创且不赞同任何抄袭行为。如果写作者在文章中引用的素材涉嫌侵权行为，请作者与原作者协商联系。如果有任何侵权行为，原作者有权要求作者删除侵害内容。</p>
            <p>如果用户从第三方导入至本平台的素材内容依旧归原作者所有。如果涉及侵权，权益受害者可向本平台举报申诉，本平台有权对删除内容。</p>
            <p class="purple">用户使用Get智能写作工具所创作的最终内容归用户所有。关于创作的内容任何法律后果及风险由写作者个人承担。请务必尊重版权。</p>
        </div>
        <MyFooter />
    </div>
</template>
<script>
import PageHeader from "~/components/Header.vue"
import MyFooter from '~/components/Footer.vue'
export default {
  components: { PageHeader, MyFooter },    
}
</script>
<style lang="scss" scoped>
.wrapper-box {
    min-height: 100vh;
    background-color: #fff;
    .content {
        min-height: 100vh;
        text-align: center;
        margin: 0 35%;
        h1 {
            margin-top: 100px;
            margin-bottom: 40px;
            font-weight: bold;
        }
        P {
            margin-bottom: 26px;
        }
        .purple {
            color: #4A25BA;
        }
    }
}
</style>
