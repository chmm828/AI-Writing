<template>
    <div class="wrapper-box">
        <PageHeader bgcolor="#4A25BA" color="white"/>
        <div class="content">
            <div class="title">
                <h1>公司介绍</h1>
            </div>
            <div class="info">
                <p><strong>我们是谁：</strong></p>
            </div>
            <br>
            <div class="intro-box">
                <p>我们是字语未来（深圳）智能科技有限公司，成立于2022年11月，是一家定义人机协作的公司，目前主打智能写作产品「Get智能写作」。</p>
                <p>Get智能写作，用AI加速写作，以人机协作的方式提升写作效率，快速创作爆款文章。Get-AI提供精准文章素材、10w+文章模板、智能取标题、一键智能改写等功能。减少80%的重复脑力劳动，释放大脑，专注创作。在人工智能时代，Get要让人人都能出可以赚钱的爆款文章。</p>
                <p>我们为企业提供文本挖掘、知识图谱、知识搜索引擎、知识库等文本及知识智能处理的技术服务，提升企业在专有技术（KnowHow）以及团队培养方面的效率与竞争力。</p>
                <p>我们的核心功能有「AI 模板成文」、「AI 热点成文」、「AI创作」、「智能改写」、「质量检测」等 AI 核心功能。</p>
            </div>
            <br>
            <div class="info">
                <p><strong>我们的使命：</strong></p>
            </div>
            <br>
            <p>Get智能写作平台，让 AI 助你轻松创作10w+文章</p>
            <br>
<!--            <div class="info">-->
<!--                <p><strong>公司地址：</strong></p>-->
<!--            </div>-->
<!--            <br>-->
<!--            <p>上海市静安区光复路1号</p>-->
<!--            <p>邮箱: zhangyun@xuehuiget.com</p>-->
<!--            <p>Tel: 021-80158414</p>-->
<!--            <br>-->
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
    h1, h2 {
        font-weight: bold;
    }
    .content {
        margin: 0 29%;
        margin-top: 70px;
        min-height: calc(100vh - 381px);
        .title {
            margin-bottom: 50px;
            text-align: center;
        }
    }
    .intro-box {
        p {
            // text-indent: 2em;
        }
    }
}
</style>
