<template lang="pug">
  #app.container-fluid.row
    router-view

    .footer.col-lg-12.col-md-12.col-sm-12.col-xs-12
      .pull-left
        .app-name quizar - {{ version }}

      .pull-right
        .menu
          li
            router-link(to='/') #[i.fa.fa-book]
          li(v-if='version !== "eleve"')
            router-link(to='/manage') #[i.fa.fa-database]
          li
            router-link(to='/export-import') #[i.fa.fa-share-alt]
</template>

<script>
  import store from 'renderer/vuex/store'

  export default {
    data () {
      return {
        env: process.env.NODE_ENV,
        version: process.env.VERSION,
        dbLocation: '.data',
        key: 'llsfeuldm'
      }
    },
    store,
    created () {
      /** if this is the production environment, we adjust the dbLocation variable */
      if (this.env !== 'development') {
        this.dbLocation = 'resources/app/dist/.data'
      }

      /** If the version is not defined, it means we are in a development environment */
      if (this.version === undefined) {
        this.version = 'development'
      }

      this.createEmpty()
    },
    methods: {
      /** Global methods */
      getData: function () {
        const fs = require('fs')
        const cryptoJS = require('crypto-js')

        /** decrypting the JSON database */
        let db = fs.readFileSync(this.dbLocation, 'utf8')
        let raw = cryptoJS.AES.decrypt(db, this.key)
        let decrypted = JSON.parse(raw.toString(cryptoJS.enc.Utf8))

        return decrypted
      },
      createEmpty: function () {
        if (!this.dbExist()) {
          /** if the database couldn't be found, create it */
          let empty = JSON.stringify([])

          this.writeDb(empty)
        }
      },
      writeDb: function (write) {
        const fs = require('fs-extra')
        const cryptoJS = require('crypto-js')

        /** encrypting the JSON database */
        let crypted = cryptoJS.AES.encrypt(write, this.key)

        fs.writeFileSync(this.dbLocation, crypted, 'utf8')
      },
      dbExist: function () {
        const fs = require('fs')

        return fs.existsSync(this.dbLocation)
      },
      escape: function (string) {
        var escape = document.createElement('textarea')
        escape.textContent = string

        return escape.innerHTML
      },
      nl2br: function (str, isXhtml) {
        var breakTag = (isXhtml || typeof isXhtml === 'undefined') ? '<br />' : '<br>'
        return (str + '').replace(/([^>\r\n]?)(\r\n|\n\r|\r|\n)/g, '$1' + breakTag + '$2')
      }
    }
  }
</script>

<style lang="scss">
$icon-font-path: "~bootstrap-sass/assets/fonts/bootstrap/";
@import "~bootstrap-sass/assets/stylesheets/_bootstrap.scss";

/** Importing variables file */
@import 'sass/variables.scss';

  /** webkit hacks */
  ::-webkit-scrollbar {
    display: none;
  }

  input::-webkit-outer-spin-button {
    width: 200px;
  }

  body {
    min-width: $min-width;
    font-family: $font-family;
  }

  .container-fluid {
    /** giving space for the footer */
    padding: $header-height 0 ($footer-height + 10px) 0;
  }

  .row {
    margin: 0;
  }

  li {
    list-style: none;
  }

  .disabled {
    &:hover {
      cursor: not-allowed;
    }
  }

  .btn {
    border-radius: $button-border-radius;
    border: $button-border;
    box-shadow: $button-shadow;

    text-transform: uppercase;
    font-weight: bold;
    font-size: $button-font-size;
    transition: $transition;

    &:focus {
      outline: none !important;
      box-shadow: inherit;
    }

    &:active {
      outline: none !important;
      box-shadow: inherit;
    }
  }

  .btn-danger {
    background: $color2
  }

  .btn-lg {
    padding: $button-lg-padding;
  }

  .btn-sm {
    padding: $button-sm-padding;
  }

  .btn-xs {
    padding: $button-xs-padding;
  }

  .bubble {
    float: left;
    position: relative;
    bottom: 40px;

    font-size: 14px;
    padding: 10px;

    background: orange;
    color: white;
    border-radius: 5px;
  }

  .block {
    padding: $block-padding;
    background: $block-background;
    border-radius: $block-border-radius;
    box-shadow: $block-shadow;

    color: $block-text-color;

    .form-control {
      border: none;
      border-radius: 2px;
      box-shadow: $button-shadow;
      transition: $transition;
      text-align: center;

      &:focus {
        box-shadow: $form-focus-shadow;
      }
    }
  }

  .block-alert {
    position: fixed;
    bottom: 50%;
    left: 50%;
    margin-left: $alert-margin-left;
    margin-bottom: $alert-margin-bottom;
    width: $alert-width;
    
    background: $block-alert-background;
    border-radius: $block-border-radius;
    box-shadow: $block-shadow;
    padding: $block-padding + 7px;

    color: $block-text-color;
    font-weight: bold;
    text-transform: uppercase;
  }

  .footer {
    position: fixed;
    bottom: 0;

    height: $footer-height;
    padding: $footer-padding;
    box-shadow: $footer-shadow;
    text-shadow: $menu-shadow;
    
    background: $footer-background;

    .menu {
      float: right;

      li {
        list-style: none;
        display: inline;

        padding: $footer-menu-button-padding;
        font-size: $footer-menu-button-font-size;

        a {
          color: $footer-menu-button-color;
          transition: $transition;

          &:hover {
            color: $footer-menu-button-color-current;
          }
        }

        .router-link-exact-active {
          color: $footer-menu-button-color-current;
        }
      }
    }

    .app-name {
      font-weight: $app-font-weight;
      font-size: $app-font-size;
      color: $app-font-color;

      text-transform: uppercase;
    }
  }
</style>
