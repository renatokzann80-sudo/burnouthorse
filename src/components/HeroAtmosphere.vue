<script setup>
import { onMounted, onUnmounted, ref } from 'vue'
const canvas=ref(null)
let gl,program,frame,resize
const vertex=`attribute vec2 position;varying vec2 uv;void main(){uv=position*.5+.5;gl_Position=vec4(position,0.,1.);}`
const fragment=`precision highp float;varying vec2 uv;uniform vec2 resolution;uniform float time;uniform vec2 eyeLeft;uniform vec2 eyeRight;uniform vec2 eyeSize;
float hash(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5453);}
float rain(vec2 p,float scale,float speed,float thickness){p.x*=resolution.x/resolution.y;p*=scale;p.x+=p.y*.16;vec2 cell=floor(p);vec2 f=fract(p);float rnd=hash(cell);float y=fract(f.y+time*speed+rnd);float x=abs(f.x-.5-(rnd-.5)*.48);return smoothstep(thickness,0.,x)*smoothstep(.94,.12,y)*smoothstep(0.,.08,y)*step(.43,rnd);}
void main(){
float farRain=rain(uv+vec2(.13,.04),48.,1.08,.011)*.16;
float midRain=rain(uv+vec2(.31,.17),31.,.74,.016)*.24;
float nearRain=rain(uv,17.,.47,.024)*.31;
float r=farRain+midRain+nearRain;
float floorMask=smoothstep(.48,.02,uv.y);
float ripples=pow(.5+.5*sin(uv.x*62.+sin(uv.y*21.)*4.-time*2.),9.)*floorMask*.10;
float windowGlow=exp(-distance(uv,vec2(.79,.73))*4.8)*(.035+.012*sin(time*.85));
float mist=(hash(floor(uv*vec2(18.,9.)+time*.08))-.5)*.025;
vec3 color=vec3(.78,.88,.87)*r+vec3(.92,.62,.27)*(ripples+windowGlow)+vec3(.62,.70,.68)*mist;
float alpha=clamp(r*.78+ripples*.30+windowGlow*.24+abs(mist),0.,.44);
float blinkPhase=mod(time+1.35,5.7);
float blink=smoothstep(4.92,5.03,blinkPhase)*(1.-smoothstep(5.12,5.25,blinkPhase));
float leftLid=1.-smoothstep(.78,1.,length((uv-eyeLeft)/eyeSize));
float rightLid=1.-smoothstep(.78,1.,length((uv-eyeRight)/eyeSize));
float lids=max(leftLid,rightLid)*blink;
color=mix(color,vec3(.50,.24,.12),lids);
alpha=max(alpha,lids*.96);
gl_FragColor=vec4(color,alpha);}`
const compile=(type,source)=>{const shader=gl.createShader(type);gl.shaderSource(shader,source);gl.compileShader(shader);return shader}
onMounted(()=>{if(matchMedia('(prefers-reduced-motion: reduce)').matches)return;gl=canvas.value.getContext('webgl',{alpha:true,antialias:true,premultipliedAlpha:false});if(!gl)return;program=gl.createProgram();gl.attachShader(program,compile(gl.VERTEX_SHADER,vertex));gl.attachShader(program,compile(gl.FRAGMENT_SHADER,fragment));gl.linkProgram(program);gl.useProgram(program);const buffer=gl.createBuffer();gl.bindBuffer(gl.ARRAY_BUFFER,buffer);gl.bufferData(gl.ARRAY_BUFFER,new Float32Array([-1,-1,1,-1,-1,1,-1,1,1,-1,1,1]),gl.STATIC_DRAW);const position=gl.getAttribLocation(program,'position');gl.enableVertexAttribArray(position);gl.vertexAttribPointer(position,2,gl.FLOAT,false,0,0);const resolution=gl.getUniformLocation(program,'resolution'),time=gl.getUniformLocation(program,'time'),eyeLeft=gl.getUniformLocation(program,'eyeLeft'),eyeRight=gl.getUniformLocation(program,'eyeRight'),eyeSize=gl.getUniformLocation(program,'eyeSize');resize=()=>{const rect=canvas.value.getBoundingClientRect(),ratio=Math.min(devicePixelRatio||1,2),sw=1680,sh=943,scale=Math.max(rect.width/sw,rect.height/sh),rw=sw*scale,rh=sh*scale,mobile=rect.width<=820,ox=mobile?(rect.width-rw)*.76:(rect.width-rw)/2,oy=mobile?0:(rect.height-rh)/2;canvas.value.width=rect.width*ratio;canvas.value.height=rect.height*ratio;gl.viewport(0,0,canvas.value.width,canvas.value.height);gl.uniform2f(resolution,canvas.value.width,canvas.value.height);gl.uniform2f(eyeLeft,(ox+1307*scale)/rect.width,1-(oy+224*scale)/rect.height);gl.uniform2f(eyeRight,(ox+1363*scale)/rect.width,1-(oy+224*scale)/rect.height);gl.uniform2f(eyeSize,14*scale/rect.width,8*scale/rect.height)};resize();addEventListener('resize',resize);const start=performance.now();const render=now=>{gl.uniform1f(time,(now-start)/1000);gl.clearColor(0,0,0,0);gl.clear(gl.COLOR_BUFFER_BIT);gl.drawArrays(gl.TRIANGLES,0,6);frame=requestAnimationFrame(render)};frame=requestAnimationFrame(render)})
onUnmounted(()=>{cancelAnimationFrame(frame);if(resize)removeEventListener('resize',resize);gl?.getExtension('WEBGL_lose_context')?.loseContext()})
</script>
<template><div class="hero-atmosphere"><canvas ref="canvas"></canvas></div></template>
<style scoped>
.hero-atmosphere,.hero-atmosphere canvas{position:absolute;inset:0;width:100%;height:100%;pointer-events:none}.hero-atmosphere{z-index:3;overflow:hidden}.hero-atmosphere canvas{display:block;mix-blend-mode:screen;opacity:.9}@media(max-width:820px){.hero-atmosphere{height:590px}}
</style>
