<template>
  <div class="home-blog" :class="recoShow?'reco-show': 'reco-hide'">
    <div class="hero" :style="{background: `url(${$page.frontmatter.bgImage || require('../images/home-bg.jpg')}) center/cover no-repeat`}">
      <h1>{{ data.heroText || $title || '午后南杂' }}</h1>

      <p class="description">{{ data.tagline || $description || 'Welcome to your vuePress-theme-reco site' }}</p>
      <p class="huawei" v-if="$themeConfig.huawei !== false"><i class="iconfont reco-huawei" style="color: #fc2d38"></i>&nbsp;&nbsp;&nbsp;华为，为中华而为之！</p>
    </div>

    <!-- <div class="features" v-if="data.features && data.features.length">
      <div v-for="(feature, index) in data.features" :key="index" class="feature">
        <h2>{{ feature.title }}</h2>
        <p>{{ feature.details }}</p>
      </div>
    </div>

    <h1 class="home-blog-title">最近</h1> -->
    <div class="home-blog">
      <!-- 博客列表 -->
      <note-abstract 
        class="blog-list"
        :data="posts"
        :currentPage="1"></note-abstract>
      <div class="info-wrapper">
         <img class="personal-img" :src="$page.frontmatter.faceImage || $themeConfig.logo" alt="hero"> 
         <h3 class="name" v-if="$themeConfig.author || $site.title">{{ $themeConfig.author || $site.title }}</h3>
         <div class="num">
           <div>
             <i class="iconfont reco-category"></i> {{$categories.length}}
           </div>
           <div>
             <i class="iconfont reco-tag"></i> {{$tags.length}}
           </div>
         </div>
      </div>  
    </div>

    

    <Content class="home-center" custom/>

    <div class="footer">
      <span>
        <i class="iconfont reco-theme"></i>
        <a target="blank" href="https://vuepress-theme-reco.recoluan.com">VuePress-theme-reco</a>
      </span>
      <span v-if="$themeConfig.record">
        <i class="iconfont reco-beian"></i>
        <a>{{ $themeConfig.record }}</a>
      </span>
      <span>
        <i class="iconfont reco-copyright"></i>
        <a>
          <span v-if="$themeConfig.startYear">{{ $themeConfig.startYear }} - </span>
          {{ year }}
          &nbsp;&nbsp;
          <span v-if="$themeConfig.author || $site.title">{{ $themeConfig.author || $site.title }}</span>
          </a>
      </span>
      <span>
        <AccessNumber idVal="/"></AccessNumber>
      </span>
    </div>
  </div>
</template>

<script>
import NavLink from "@theme/components/NavLink/";
import AccessNumber from '@theme/components/Valine/AccessNumber'
import NoteAbstract from '@theme/components/NoteAbstract.vue'
import { constants } from 'fs';

export default {
  components: { NavLink, AccessNumber, NoteAbstract },
  data () {
    return {
      recoShow: false
    }
  },
  computed: {
    // 时间降序后的博客列表
    posts () {
      let posts = this.$site.pages
      posts = posts.filter(item => {
        const { home, isTimeLine, date } = item.frontmatter
        return !(home == true || isTimeLine == true || date === undefined)
      })
      posts.sort((a, b) => {
        return this._getTimeNum(b) - this._getTimeNum(a)
      })
      return posts
    },
    year () {
      return new Date().getFullYear()
    },
    data() {
      return this.$page.frontmatter;
    },

    actionLink() {
      return {
        link: this.data.actionLink,
        text: this.data.actionText
      };
    },

    heroImageStyle () {
      return this.data.heroImageStyle || {
        maxHeight: '200px',
        margin: '6rem auto 1.5rem'
      }
    }
  },
  mounted () {
    this.recoShow = true
  },
  methods: {
    // 根据分类获取页面数据
    getPages () {
      let pages = this.$site.pages
      pages = pages.filter(item => {
        const { home, isTimeLine, date } = item.frontmatter
        return !(home == true || isTimeLine == true || date === undefined)
      })
      // reverse()是为了按时间最近排序排序
      this.pages = pages.length == 0 ? [] : pages
    },
    // 获取时间的数字类型
    _getTimeNum (data) {
      return parseInt(new Date(data.frontmatter.date).getTime())
    }
  }
};
</script>

<style lang="stylus">
@require '../styles/loadMixin.styl'

.home-blog {
  padding: $navbarHeight 0 0;
  margin: 0px auto;

  .hero {
    min-height 350px
    text-align: center;
    overflow hidden
    figure {
      position absolute
      background yellow
    }

    h1 {
      margin: 4rem auto 1.8rem ;
      font-size: 2.5rem;
      color #fff
    }

    h1, .description, .action, .huawei {
      color #fff!important
    }

    .description {
      margin: 1.8rem auto;
      font-size: 1.6rem;
      line-height: 1.3;
      color: lighten($textColor, 20%);
    }
  }
  .home-blog-title {
    margin 0 auto 10px
    max-width 960px
  }
  .home-blog {
    display flex
    align-items: flex-start;
    margin 20px auto 0
    max-width 1126px
    .info-wrapper {
      transition all .3s
      margin-left 15px;
      width 380px;  
      height auto;
      box-shadow 0 2px 10px rgba(0,0,0,0.2);
      &:hover {
        box-shadow: 0 4px 20px 0 rgba(0,0,0,0.2);
      }
      .personal-img {
        display block
        margin 2rem auto
        width 8rem
        height 8rem
      }
      .name {
        text-align center
      }
      .num {
        display flex
        margin 0 auto 1rem
        width 80%
        > div {
          text-align center
          flex auto
          &:first-child {
            border-right 1px solid #333
          }
          i {
            margin-right .2rem
          }
        }
      }
    }
  }

  .features {
    max-width 1126px
    padding: 1.2rem 0;
    margin: 2.5rem auto 0;
    display: flex;
    flex-wrap: wrap;
    align-items: flex-start;
    align-content: stretch;
    justify-content: space-between;
  }

  .feature {
    flex-grow: 1;
    flex-basis: 30%;
    max-width: 32%;
    transition: all .5s
    box-sizing border-box
    margin-bottom 10px
    padding 0 15px
    box-shadow 0 2px 10px rgba(0,0,0,0.2)
    h2 {
      font-size: 1.6rem;
      font-weight: 500;
      border-bottom: none;
      padding-bottom: 0;
      color: lighten($textColor, 10%);
    }

    p {
      color: lighten($textColor, 20%);
    }

    &:hover {
      transform scale(1.05)
    }
  }

  .footer {
    padding: 2.5rem;
    border-top: 1px solid $borderColor;
    text-align: center;
    color: lighten($textColor, 25%);
    load-start()
    > span {
      margin-left 1rem
      > i {
        margin-right .5rem
      } 
    }
  }
}

&.reco-hide {
  .hero {
    img {
      load-start()
    }
    .h1 {
      load-start()
    }
    .description {
      load-start()
    }
    .huawei {
      load-start()
    }
    .action-button {
      load-start()
    }
  }
  .features {
    load-start()
  }
  .home-center {
    load-start()
    padding 0
  }
  .footer {
    load-start()
  }
}

&.reco-show {
  .hero {
    img {
      load-end(0.08s)
    }
    .h1 {
      load-end(0.16s)
    }
    .description {
      load-end(0.24s)
    }
    .huawei {
      load-end(0.32s)
    }
    .action-button {
      load-end(0.4s)
    }
  }
  .features {
    load-end(0.40s)
  }
  .home-center {
    load-end(0.48s)
  }
  .footer {
    load-end(0.56s)
  }
}

@media (max-width: $MQMobile) {
  .home {
    padding-left: 1.5rem;
    padding-right: 1.5rem;
    .hero {
      margin 0 -1.5rem
      min-height 350px
      img {
        max-height: 210px;
        margin: 2rem auto 1.2rem;
      }

      h1 {
        margin: 6rem auto 1.8rem ;
        font-size: 2rem;
      }

      h1, .description, .action {
        // margin: 1.2rem auto;
      }

      .description {
        font-size: 1.2rem;
      }

      .action-button {
        font-size: 1rem;
        padding: 0.6rem 1.2rem;
      }
    }
    .features {
      flex-direction: column;
    }

    .feature {
      max-width: 100%;
      padding: 0 2.5rem;
    }
    .home-blog {
      .info-wrapper {
        display none
      }
    }
  }
  .footer {
    text-align: left!important;
    > span {
      display block
      margin-left 0
      line-height 2rem
    }
  }
}

@media (max-width: $MQMobileNarrow) {
  .home {
    padding-left: 1.5rem;
    padding-right: 1.5rem;

    .hero {
      margin 0 -1.5rem
      min-height 350px
      img {
        max-height: 210px;
        margin: 2rem auto 1.2rem;
      }

      h1 {
        margin: 6rem auto 1.8rem ;
        font-size: 2rem;
      }

      h1, .description, .action {
        // margin: 1.2rem auto;
      }

      .description {
        font-size: 1.2rem;
      }

      .action-button {
        font-size: 1rem;
        padding: 0.6rem 1.2rem;
      }
    }

    .feature {
      h2 {
        font-size: 1.25rem;
      }
    }

    .home-blog {
      .info-wrapper {
        display none
      }
    }
  }
}
</style>
