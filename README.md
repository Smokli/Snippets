http://jsfiddle.net/YNBxz/2/




<!DOCTYPE html>
<head>
	<script src="jquery-2.1.4.js"></script>
	
	<style>
		#control-panel input[type="number"] {
			width:50px;
			}
			
			.menu-box{
				display:inline-block;
				width:65px;
				}
			
			#crossword-wrapper{
				margin:10px;
				}
			
			table{
				border-collapse:collapse;
				border:1px solid #999999;
				width:90%;
				}
			
			td{
				border:1px solid #999999;
				cursor:pointer;
				text-align:center;
				font-weight:bold;
				}
			
			tbody td{
				background-color:#FFF7BD;
				}
			.selected-letter{
				background-color:#BFFA66;
				}
				
				td span{
					opacity:0;
					width:100%;
					display:inline-block;
					font-size:120px;
					}
					
					.edit-letter{
						opacity:1;
						}
					
					.revealed-letter{
						background-color:#A5D4E8;
						}
			
		</style>
	</head>

<body>
	<button id="menu">Menu</button>
	<div id="control-panel">
		<div class="menu-box">width:</div><input type="number" value="10" min="5" max="500">
		<div class="menu-box">font-size:</div><input type="number" id="font-size" value="120" min="5" max="500">
		<br>
		<div class="menu-box">height:</div><input type="number" value="5" min="3" max="300">
		
		<div>
				<button id="generate-crossword">Generate crossword</button>
				<button id="edit-crossword" class="disabled" disabled>Edit</button>
				<button id="clear-crossword" class="disabled" disabled>Clear</button>
				<button id="start-game" class="disabled" disabled>Start</button>
			</div>
		</div>
	
	
	
	<script src="test.js"></script>
	</body>
</html>









var enableEdit = false;
var showMenu = true;
var isFirst = true;

$(document).ready(function(){
	
	$('#generate-crossword').click(function(){
		var x = parseInt($('#control-panel input[type="number"]').eq(0).val()),
				y = parseInt($('#control-panel input[type="number"]').eq(2).val());
				
				x = x > 500 ? 500 : (x < 5 ? 5 : x);
				y = y > 300 ? 300 : (y < 3 ? 3 : y);
				generateCrossword(x,y);
				
				
				document.querySelector('#font-size').addEventListener('input', function(){
					$('span').css('font-size', Number(this.value));
				});
		});
	});
	
	$('#menu').click(function(){
		showMenu = !showMenu;
		$('#control-panel').css('display', (showMenu ? '' : 'none'));
		});
	
	
	function generateCrossword(x,y){
		if($('#crossword-wrapper')){
			$('#crossword-wrapper').remove();
			}
		var $prevLetter = $();
		
	$(document.body).append('<div id="crossword-wrapper"></div>');
	
	$('#crossword-wrapper').append('<table id="crossword-puzzle"></table>');
		
	$('table').prepend('<thead><tr><th colspan="' + x + '">Crossword</th></tr></thead>');
	$('table').css('height', window.innerHeight);
	
	for(var a = 0; a < y; a++){
		var row = $('<tr>');
		
		for(var b = 0; b < x; b++){
			row.append('<td><span>*</span></td>');
			}
		$('table').append(row);
		}
	$('td').css('width', $('table').width() / $('tr:nth-child(1) td').length);
	$('td').click(function(ev){
		
		if(enableEdit){
			return;
			}
		
		if(this.className === 'selected-letter' && confirm('Ama na 100%?')){
			
			if(isFirst){
				//TO DO text za purva bukva;
				}
				isFirst = false;
			$(this).removeClass('selected-letter');
			$prevLetter = $();
			$(this).find('span').animate({'opacity': 1}, 1000);
			$(this).addClass('revealed-letter');
			
			if(this.textContent == '*'){
				//TO DO text za bomba
				$(this).children().css('color', '#FA5151');
				}
				
			return;
		}else if($prevLetter.length !== 0){
			$prevLetter.removeClass('selected-letter');
		}
		
		$(this).addClass('selected-letter');
		$prevLetter = $(this);
		});
		
			
	$('#edit-crossword').click(function(){
		if(!enableEdit){
	  $('td').attr('class', '');
		$('span').attr('class', 'edit-letter');
		$('.edit-letter').attr('contenteditable', 'true');
		}
		enableEdit = true;
		$('#clear-crossword').attr('disabled', false);
		});
		
		$('#clear-crossword').click(function(){
			$('span').html('');
			});
		
	$('#start-game').click(function(){
		enableEdit = false;
		$('span').attr('class', '');
		$('span').attr('contenteditable', 'false');
		$('#clear-crossword').attr('disabled', true);
		});
		
		$('.disabled').removeAttr('disabled');
		$('#clear-crossword').attr('disabled', true);
		setHeights();
}


function setHeights(){
	var height = $('td').height();
	$('span').css('height', height);
	}
	
