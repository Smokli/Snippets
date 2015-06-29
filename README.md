var enableEdit = false;

$(document).ready(function(){
	
	$('#generate-crossword').click(function(){
		var x = parseInt($('#control-panel input[type="number"]').eq(0).val()),
				y = parseInt($('#control-panel input[type="number"]').eq(2).val());
				
				x = x > 500 ? 500 : (x < 5 ? 5 : x);
				y = y > 300 ? 300 : (y < 3 ? 3 : y);
				generateCrossword(x,y);
		});
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
	
document.querySelector('#font-size').addEventListener('input', function(){
	console.log(this.value);
	$('span').css('font-size', Number(this.value));
});
