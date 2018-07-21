<template>
  <Page class="page">
    <ActionBar class="action-bar" title="Hash Password" />
    <StackLayout class="form text-center m-20">
      <TextField class="input input-rounded m-b-20" hint="Base Password", v-model="base" />
      <TextField class="input input-rounded m-b-20" hint="Salt", v-model="salt" />
      <TextField class="input input-rounded m-b-20" hint="Hashed Password", :text="hashed", editable=false />
      <Button class="btn btn-primary btn-rounded-lg" text="Copy" @tap="copy"></Button>
    </StackLayout>
  </Page>
</template>

<script>
  import sjcl from 'sjcl'
  import bigInt from 'big-integer'
  import clipboard from 'nativescript-clipboard'

  export default {
    data () {
      return {
        base: '',
        salt: ''
      }
    },
    computed: {
      hashed () {
        let digest = sjcl.hash.sha256.hash(this.base + this.salt)
        let hex = sjcl.codec.hex.fromBits(digest)
        let chars = [
          '0', '1', '2', '3', '4', '5', '6', '7', '8', '9',
          'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M',
          'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
          'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm',
          'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z',
          '~', '!', '@', '#', '$', '%', '^', '&', '*', '(', ')', '_', '+',
          '{', '}', '|', ':', '"', '<', '>', '?', '-', '=', '[', ']', ';',
          ',', '.', '/', '\\', '\''
        ]

        return bigInt(hex.substring(0, 24), 16).toArray(chars.length).value.map(code => chars[code]).join('')
      }
    },
    methods: {
      copy () {
        clipboard.setText(this.hashed).then(() => console.log('Copied!'))
      }
    }
  }
</script>

<style scoped>
  .btn {
    width: 100%;
    height: 50;
    border-radius: 25;
  }
</style>
