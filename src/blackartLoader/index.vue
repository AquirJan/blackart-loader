<template>
  <div v-if="busying && !instance">
    <svg class="blackart-icon loading" :style="loadingIconStyle">
      <use xlink:href="#bi-spinner"></use>
    </svg>
    <!-- icons -->
    <symbol id="bi-spinner" viewBox="0 0 32 32">
      <title>spinner</title>
      <path d="M32 16c-0.040-2.089-0.493-4.172-1.331-6.077-0.834-1.906-2.046-3.633-3.533-5.060-1.486-1.428-3.248-2.557-5.156-3.302-1.906-0.748-3.956-1.105-5.981-1.061-2.025 0.040-4.042 0.48-5.885 1.292-1.845 0.809-3.517 1.983-4.898 3.424s-2.474 3.147-3.193 4.994c-0.722 1.846-1.067 3.829-1.023 5.79 0.040 1.961 0.468 3.911 1.254 5.694 0.784 1.784 1.921 3.401 3.316 4.736 1.394 1.336 3.046 2.391 4.832 3.085 1.785 0.697 3.701 1.028 5.598 0.985 1.897-0.040 3.78-0.455 5.502-1.216 1.723-0.759 3.285-1.859 4.574-3.208 1.29-1.348 2.308-2.945 2.977-4.67 0.407-1.046 0.684-2.137 0.829-3.244 0.039 0.002 0.078 0.004 0.118 0.004 1.105 0 2-0.895 2-2 0-0.056-0.003-0.112-0.007-0.167h0.007zM28.822 21.311c-0.733 1.663-1.796 3.169-3.099 4.412s-2.844 2.225-4.508 2.868c-1.663 0.646-3.447 0.952-5.215 0.909-1.769-0.041-3.519-0.429-5.119-1.14-1.602-0.708-3.053-1.734-4.25-2.991s-2.141-2.743-2.76-4.346c-0.621-1.603-0.913-3.319-0.871-5.024 0.041-1.705 0.417-3.388 1.102-4.928 0.683-1.541 1.672-2.937 2.883-4.088s2.642-2.058 4.184-2.652c1.542-0.596 3.192-0.875 4.832-0.833 1.641 0.041 3.257 0.404 4.736 1.064 1.48 0.658 2.82 1.609 3.926 2.774s1.975 2.54 2.543 4.021c0.57 1.481 0.837 3.064 0.794 4.641h0.007c-0.005 0.055-0.007 0.11-0.007 0.167 0 1.032 0.781 1.88 1.784 1.988-0.195 1.088-0.517 2.151-0.962 3.156z"></path>
    </symbol>
  </div>
  <div v-else-if="!busying && !instance">
    <svg class="blackart-icon reload" :style="reloadIconStyle" @click="reloadAction">
      <use xlink:href="#bi-reload"></use>
    </svg>
    <!-- icon -->
    <symbol id="bi-reload" viewBox="0 0 32 32">
    <title>loop-alt1</title>
    <path d="M12 10h-4v-0.016c0-1.102 0.898-1.984 2-1.984h12c1.102 0 2 0.898 2 2v2h4v-2c0-3.309-2.691-6-6-6h-12c-3.309 0-6 2.691-6 6h-4l6 6 6-6zM26 16l-6 6h4v0.016c0 1.101-0.898 1.984-2 1.984h-12c-1.102 0-2-0.898-2-2v-2h-4v2c0 3.309 2.691 6 6 6h12c3.309 0 6-2.691 6-6h4l-6-6z"></path>
    </symbol>
  </div>
  <component :is="instance" v-else-if="!busying && instance" v-on="$listeners" v-bind="$attrs" ></component>
</template>
<script>
// import './style.css';
export default {
  name:'blackartLoader',
  data() {
    return {
      busying:false,
      instance:null,
      getJsNameReg:/\w+\.js$/gi,
      getLocalBBName:/\w+-v(\d+\.\d+\.\d+)/gi,
    }
  },
  props:{
    url:{
      type:String,
      default:null,
    },
    local:{
      type:Boolean,
      default:false,
    },
    async:{
      type:Boolean,
      default:false,
    },
    timeout:{
      type:Number,
      default:10000,
    },
    localReg:{
      type:Object|String,
      default:null,
    },
    loadingIconStyle:{
      type:Object,
      default:null
    },
    reloadIconStyle:{
      type:Object,
      default:null
    }
  },
  methods:{
    initCss(){
      const cssTag = document.getElementById('blackart-loader-css');
      if(!cssTag || cssTag.innerText == ''){
        let reusableCss = document.createElement('style');
        reusableCss.id = 'blackart-loader-css';
        reusableCss.type = 'text/css';
        reusableCss.innerText = '.blackart-icon{width:1.5em;height:1.5em;font-size:1.5em;display:block}.blackart-icon.loading{margin:8px auto;animation:blackartRound360 1s infinite linear}.blackart-icon.reload{margin:8px auto;cursor:pointer}@keyframes blackartRound360{from{transform:rotate(0)}to{transform:rotate(360deg)}}';
        document.getElementsByTagName("head")[0].appendChild(reusableCss)
      }
    },
    getUrlFileName(_url, _local){
      let tmp_file_name = '';
      if(!_url){
        return tmp_file_name;
      }
      if(_local){
        let final_localReg = this.getLocalBBName;
        if(this.localReg){
          if(typeof(this.localReg) === 'string'){
            final_localReg = new RegExp(this.localReg, 'gi');
          }else{
            final_localReg = this.localReg;
          }
        }
        tmp_file_name = _url.match(final_localReg);
        tmp_file_name=tmp_file_name?tmp_file_name.toString():'';
        
      }else{
        tmp_file_name = _url.match(this.getJsNameReg);
        tmp_file_name=tmp_file_name?tmp_file_name.toString():'';
      }
      return tmp_file_name;
    },
    reloadAction(){
      this.getComponent(this.url, this.local, this.async, this.timeout);
    },
    detectJsCache(_id){
      let scripts = document.getElementById(_id);
      let filecontent = ''
      if(scripts){
        filecontent = scripts.text
      }
      return filecontent;
    },
    getComponent(_url, _local, _async, _timeout){
      let fileName = this.getUrlFileName(_url, _local);
      if(!fileName || fileName===''){
        console.warn(`blackart loader warning: can not find out the file name;\n  problem url: ${_url} ;\n  is async: ${_async} ;\n  is local: ${_local} ;\n  localReg is: ${this.localReg} ;`)
        return;
      }
      let scriptContent = this.detectJsCache(fileName);
      
      if(scriptContent === ''){
        this.busying = true;
        let xmlhttp=new XMLHttpRequest();
        if(_async){
          xmlhttp.timeout = _timeout;
        }
        xmlhttp.onreadystatechange=()=>{
          if (xmlhttp.readyState==4 && xmlhttp.status==200){
            this.busying = false;
            try{
              this.instance = eval(xmlhttp.responseText);
              const script = document.createElement( 'script' );
              script.innerText = xmlhttp.responseText;
              script.setAttribute('id', fileName);
              const head = document.getElementsByTagName('head')[0]
              head.appendChild( script );
              
            }catch(e){
              console.error(e)
              console.warn('blackart loader warning: compile errro')
            }
          }
        }
        xmlhttp.open("GET", _url, _async);
        xmlhttp.send();
      }else{
        try{
          this.instance = eval(scriptContent);
        }catch(e){
          console.error(e)
          console.warn('blackart loader warning: cache compile errro')
        }
      }
    }
  },
  created(){
    this.initCss();
  },
  mounted(){
    this.getComponent(this.url, this.local, this.async, this.timeout);
  }
}
</script>

