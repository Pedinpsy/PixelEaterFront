<template>
<div  @keydown="keyPressed()" class="content" id="websocket">

   <div class="row " >
      <div class="col">
    <div class="progress" style="height: 40px;">
  <div id="progressBar" class="progress-bar progress-bar-striped progress-bar-animated" role="progressbar" style="width: 0%" aria-valuenow="10" aria-valuemin="0" aria-valuemax="100"></div>

</div>
 
    </div>

 
   </div>
     <div>&nbsp;</div>
  <div class="row " >
     <div class="col">

    </div>
    <div class="col ">
    <!-- <div class="row">
      <button id="asd" class="btn col btn-sm btn-info" @click="connect">Create connection</button>
      <button class="btn btn-sm col btn-success" @click="startTask">Start Task</button>
      <button class="btn btn-sm col btn-danger" @click="stopTask">Stop Task</button>
      <button class="btn btn-sm col  btn-primary" @click="disconnect">Close connection</button>
      </div> -->
      <canvas class="conteiner" width="800" height="800" id="gameCanva"   ></canvas>
    </div>
<div class="col-md-3 order-md-2 mb-4">

 
          <ul class="list-group mb-3">
            <li class=" bg-success  list-group-item d-flex justify-content-between lh-condensed">
              <div>
                <h6 class="my-0">Score</h6>
                
              </div>
              
            </li>
            <li v-for=" (item,index)  in playerScore " :key="index" v-bind:class ="index ==cookieName?'list-group-item d-flex justify-content-between lh-condensed text-success ' :'list-group-item d-flex justify-content-between lh-condensed'">

              <div>
              
                <small v-bind:class ="playerScore[index].index ==cookieName? 'text-primary' : 'text-dark'">{{playerScore[index].index.replace("io=","").split("-")[0] }}</small>
              </div>
              <span class="text-dark">{{playerScore[index].player.points}}</span>
            </li>
            
          
          </ul>

        </div>
  </div>
<div class="row">
 
</div>
  
 
</div>
</template>
<script src="/socket.io.js">  </script>
<script>





export default {

head(){
return{
script:[
  {hid:"io"
  ,
  src: "/socket.io.js"},
    {hid:"ios"
  ,
  src: "/moment.min.js"}
,  {
        src: "https://code.jquery.com/jquery-3.3.1.slim.min.js",
        type: "text/javascript"
      },
      {
        src:
          "https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js",
        type: "text/javascript"
      },


]

}

},
  
 data() {

   
  return {
    cookieName: 0,
    socket  : null,
    gameCanva : null,
    player : null,
    players : [],
    pixelSize : 10,
    foods:[],
    playerScore:{}
    
              }
        },
        beforeMount () {
          console.log("antes")
        


        },
        mounted(){


        window.addEventListener('keydown', this.keyPressed);

          this.configureCanva()
          
          this.socket  = io.connect('http://10.205.30.60:9094')
          this.socket.on('chatevent',function(...args){
          

          });
            this.socket.on('initilize',this.initialize);

           this.socket.on('update',this.update);        
        },
        methods: {

          keyPressed(event){
            var codes = ["KeyA","KeyS","KeyD","KeyW","ArrowRight","ArrowUp","ArrowLeft","ArrowDown"]
            if(codes.includes(event.code)){
        
              this.socket.emit("keyPressed",event.code);             

            }
            

          },

          clearCanvas:function(){
            this.configureCanva();
            var canvaContext = this.gameCanva.getContext('2d')
            canvaContext.clearRect(0, 0, canvaContext.width, canvaContext.height);


          },
          drawPixel:function(player,color){
            var context =  this.gameCanva.getContext("2d")
            context.beginPath();
            context.fillStyle = color || '#000';
      	    context.fillRect(player.x*this.pixelSize, player.y*this.pixelSize, this.pixelSize, this.pixelSize);
            context.fill();
           
          },
          update:function(args){
            this.players = args.players      
           
            this.foods = args.foods 
            var value = 1.66666666667 *args.timer
            $("#progressBar").css("width",value+"%")
          this.playerScore = []
            this.clearCanvas();
            //TODO: tirar esses fors e colcoar em funcoes especificas
              //draw players
              for( var i in  this.players){
                this.playerScore.push({index:i, player:this.players[i]})
                console.log(this.players[i])
           
              if(i == this.cookieName ){
                this.drawPixel(this.players[i],"blue")
              }else
              this.drawPixel(this.players[i],"#000")
              //draw Food
              for( var i in  this.foods){
                this.drawPixel(this.foods[i],"red")
              }

            }

               this.playerScore.sort((a,b) => {
              return a.points-b.points
            }   )
          },
          initialize:function(args){
             console.log(args)
             this.cookieName = args.coockie;
             this.player = args.player
            console.log(this)
             this.drawPixel(args.player,"#000")

          },
          configureCanva:function(){            
          this.gameCanva = $('#gameCanva').get(0);
          var canvaContext =   this.gameCanva.getContext("2d")
          canvaContext.fillStyle = "rgb(220,220,220)";
          canvaContext.fillRect(0, 0, this.gameCanva.width, this.gameCanva.height);
          
           
          },

           connect: function(){
            

              var jsonObject = {userName: "asd",message: "message"};    

              this.socket.emit('chatevent', jsonObject);
            console.log(this.socket)
          
      },
            disconnect: function(){
               
            },
            startTask: function(){
              
            },
            stopTask: function(){
               
            },
         
        }} 

</script>

<style>
html {
  overflow-x: hidden;
  height: 100%;
  width: 100%;
}
body {
  min-height: 100%;
  background-color: rgb(128,128,128);
}
.conteiner {
    
    display: flex;
    justify-content: center;
    align-items: center;
}

</style>