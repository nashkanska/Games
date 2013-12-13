Games
=====

games
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="Content-type" content="text/html; charset=utf-8">
		<title>игра "Чупещи се блокчета"</title>
		<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"></script>
		<script>
			$(function() {
				
				var canvas = $('#canvas')[0]; 
				
				var context = canvas.getContext('2d');


				var paddleX = 200;
				var paddleY = 460;

				var paddleWidth = 100;
				var paddleHeight = 15;

				var paddleDeltaX = 0;
				var paddleDeltaY = 0;

			  	function drawPaddle(){
			    	context.fillRect(paddleX,paddleY,paddleWidth,paddleHeight);
			  	}

				var ballX = 100;
				var ballY = 300;
				var ballRadius = 8;
				
				
				

				function drawBall(){
					
					context.beginPath();

					
					context.arc(ballX,ballY,ballRadius,0,Math.PI*2,true);
						
					
					context.fill();			
				}				
				
				
				var bricksPerRow = 8;								
				var brickHeight = 20;
				var brickWidth = canvas.width/bricksPerRow;

				//матрица отговаряща за подредбата на блокчетата
				 var bricks = [
					[1,1,1,1,1,1,1,2],
					[1,1,3,1,1,1,1,1],
					[2,1,2,1,2,1,2,1],
					[1,2,1,1,1,3,1,1]
				];


				
				function createBricks(){
					for (var i=0; i < bricks.length; i++) {
						for (var j=0; j < bricks[i].length; j++) {
							drawBrick(j,i,bricks[i][j]);
						}
					}
				}

			
				function drawBrick(x,y,type){	
					switch(type){ 
						case 1:
							context.fillStyle = 'orange';
							break;
						case 2:
							context.fillStyle = 'rgb(100,200,100)';						
							break;
						case 3:
							context.fillStyle = 'rgba(50,100,50,.5)';
							break;								
						default:
							context.clearRect(x*brickWidth,y*brickHeight,brickWidth,brickHeight);
							break;

					}
					if (type){
						
						context.fillRect(x*brickWidth,y*brickHeight,brickWidth,brickHeight);
						
						context.strokeRect(x*brickWidth+1,y*brickHeight+1,brickWidth-2,brickHeight-2);
					} 
				}	
				
				var score = 0;

				function displayScoreBoard(){
					
					context.fillStyle = 'rgb(50,100,50)';
					context.font = "20px Times New Roman";
	
					
					context.clearRect(0,canvas.height-30,canvas.width,30);	
				
					context.fillText('Score: '+score,10,canvas.height-5);
				}
								
				var ballDeltaX;
				var ballDeltaY;

				function moveBall(){
				

				
				if (ballY + ballDeltaY - ballRadius < 0 
				
					|| collisionYWithBricks()){
					ballDeltaY = -ballDeltaY;
					
				}

				
				if (ballY + ballDeltaY + ballRadius > canvas.height){
					endGame();
				}
				
				if ((ballX + ballDeltaX - ballRadius < 0) ||
					
				(ballX + ballDeltaX + ballRadius > canvas.width)
				
				|| collisionXWithBricks()
				){  
					ballDeltaX = -ballDeltaX;
					
				}

				
				if (ballY + ballDeltaY + ballRadius >= paddleY){
				
					if (ballX + ballDeltaX >= paddleX && 
						ballX + ballDeltaX <= paddleX + paddleWidth){
						ballDeltaY = - ballDeltaY;
						
					}
				}

				
				ballX = ballX + ballDeltaX;
				ballY = ballY + ballDeltaY;
				}
				
				function explodeBrick(i,j){
					
					bricks[i][j] --;

					if (bricks[i][j]>0){ 
					
						score++;
					} else {
					
						score += 2;		
						
					}
				}
				
				function collisionXWithBricks(){
				    var bumpedX = false;	
					for (var i=0; i < bricks.length; i++) {
						for (var j=0; j < bricks[i].length; j++) {
							if (bricks[i][j]){ 
								var brickX = j * brickWidth;
								var brickY = i * brickHeight;
								if (
								   
								    ((ballX + ballDeltaX + ballRadius >= brickX) &&
						            (ballX + ballRadius <= brickX))
						            ||
						          
									((ballX + ballDeltaX - ballRadius<= brickX + brickWidth)&&
									(ballX - ballRadius >= brickX + brickWidth))
									){		
									if ((ballY + ballDeltaY -ballRadius<= brickY + brickHeight) &&
										(ballY + ballDeltaY + ballRadius >= brickY)){													
										
										explodeBrick(i,j);

										bumpedX = true;
									}
								}
							}
						}
					}
						return bumpedX;
				}				

				function collisionYWithBricks(){

				    var bumpedY = false;
					for (var i=0; i < bricks.length; i++) {
						for (var j=0; j < bricks[i].length; j++) {
							if (bricks[i][j]){ 
								var brickX = j * brickWidth;
								var brickY = i * brickHeight;
								if (
								 
								    ((ballY + ballDeltaY - ballRadius <= brickY + brickHeight) && 
								    (ballY - ballRadius >= brickY + brickHeight))
								    ||
								   
									((ballY + ballDeltaY + ballRadius >= brickY) &&
									(ballY + ballRadius <= brickY ))){
									if (ballX + ballDeltaX + ballRadius >= brickX && 
										ballX + ballDeltaX - ballRadius<= brickX + brickWidth){										
									
										explodeBrick(i,j);							
										bumpedY = true;
									}						
								}
							}
						}
					}
					return bumpedY;
				}
				
				function animate () {
					context.clearRect(0,0,canvas.width,canvas.height);
					
					createBricks();
					displayScoreBoard();
					
					moveBall();
					movePaddle();
					
					drawPaddle();
					drawBall();
					
					
				}
				
				var paddleDeltaX;
				var paddleSpeedX = 10;

				function movePaddle(){
					if (paddleMove == 'LEFT'){
						paddleDeltaX = -paddleSpeedX;
					} else if (paddleMove == 'RIGHT'){
						paddleDeltaX = paddleSpeedX;
					} else {
						paddleDeltaX = 0;
					}
					
					if (paddleX + paddleDeltaX < 0 || paddleX + paddleDeltaX +paddleWidth >canvas.width){
						paddleDeltaX = 0; 
					}
					paddleX = paddleX + paddleDeltaX;
				}
				
				var gameLoop;
				var paddleMove;
				
				function startGame(){
					ballDeltaY = -4;
					ballDeltaX = -2;
					paddleMove = 'NONE';
					paddleDeltaX = 0;

					
					gameLoop = setInterval(animate,20);

					
					$(document).keydown(function(evt) {
						if (evt.keyCode == 39) {
							paddleMove = 'RIGHT';
						} else if (evt.keyCode == 37){
							paddleMove = 'LEFT';
						}
					});			

					$(document).keyup(function(evt) {
						if (evt.keyCode == 39) {
							paddleMove = 'NONE';
						} else if (evt.keyCode == 37){
							paddleMove = 'NONE';
						}
					}); 
				}

				function endGame(){
					clearInterval(gameLoop);
					context.fillText('Ти загуби !',canvas.width/2-30,canvas.height/2);
				}
				
				startGame();		
			});
		</script>
	</head>
	<body>	
		<style type="text/css">
			canvas {
				border:1px solid black;
			}
		</style>
		<h1>игра "Чупещи се блокчета"</h1>
			<canvas id="canvas" width="400" height="500">
				браузъра не поддържа html5 canvas
			</canvas>	
	</body>
</html>
