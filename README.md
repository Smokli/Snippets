var enableEdit = false;

$(document).ready(function(){
	
	$('#generate-crossword').click(function(){
		var x = parseInt($('#dimentions input').eq(0).val()),
				y = parseInt($('#dimentions input').eq(1).val());
				
				x = x > 500 ? 500 : (x < 5 ? 5 : x);
				y = y > 300 ? 300 : (y < 3 ? 3 : y);
				generateCrossword(x,y);
		});
	});
	
	
	function generateCrossword(x,y){
		var $prevLetter = $();
		
	$(document.body).append('<div id="crossword-wrapper"></div>');
	
	$('#crossword-wrapper').append('<table id="crossword-puzzle"></table><div id="control-panel"></div>');
	$('#control-panel').append('<button id="edit-crossword">Edit</button><button id="clear-crossword">Clear</button><button id="start-game">Start</button>');
	
	console.log(window.innerHeight)
	$('table').prepend('<thead><tr><th colspan="' + x + '">Crossword</th></tr></thead>');
	$('table').css('height', window.innerHeight);
	
	for(var a = 0; a < y; a++){
		var row = $('<tr>');
		
		for(var b = 0; b < x; b++){
			row.append('<td><span>111</span></td>');
			}
		$('table').append(row);
		}
	
	$('td').click(function(ev){
		
		if(this.className === 'selected-letter'){
			$(this).removeClass('selected-letter');
			$prevLetter = $();
			$(this).find('span').animate({'opacity': 1}, 600);
			}else if($prevLetter.length !== 0){
			$prevLetter.removeClass('selected-letter');
			}
			$(this).addClass('selected-letter');
			$prevLetter = $(this);
		});
		
		
		
	
	$('#edit-crossword').click(function(){
		enableEdit = true;
		});
	$('#start-game').click(function(){
		enableEdit = false;
		});
		
		
}
	
