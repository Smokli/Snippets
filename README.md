<!DOCTYPE html>
<head>
	<script src="jquery-2.1.4.js"></script>
	<meta charset="UTF-8">
	<style>
	body * {
		font-family: sans-serif;
	}
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
				-webkit-user-select: none;  /* Chrome all / Safari all */
				-moz-user-select: none;     /* Firefox all */
				-ms-user-select: none;      /* IE 10+ */
				user-select: none;          /* Likely future */  
				}
			
			td{
				border:1px solid #999999;
				text-align:center;
				font-weight:bold;
				}
				td:not(.indexer){
					cursor:pointer;
					}
					table tr .num-indexer{
						cursor:initial;
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
					}
					
					.edit-letter{
						opacity:1;
						}
					
					.revealed-letter{
						background-color:#A5D4E8;
						}
						.num-indexer{
							width:30px;
							}
			
				img {
    			-webkit-transition: -webkit-transform 3s ease-out;
				}

		</style>
	</head>

<body>
	<button id="menu">Menu</button>
	<div id="control-panel">
		<div class="menu-box">width:</div><input type="number" value="16" min="5" max="500">
		<div class="menu-box">font-size:</div><input type="number" id="font-size" value="60" min="5" max="500">
		<br>
		<div class="menu-box">height:</div><input type="number" value="7" min="3" max="300">
		
		<div>
				<button id="generate-crossword">Generate crossword</button>
				<button id="edit-crossword" class="disabled" disabled>Edit</button>
				<button id="clear-crossword" class="disabled" disabled>Clear</button>
				<button id="start-game" class="disabled" disabled>Start</button>
				<button id="save-template">Save template</button>
				<input type="file" id="load-template">
			</div>
		</div>
	
	<button id="wheel">Колелото на късмета</button><input type="password" id="pass-field" style="display:none; width:80px;" placeholder="парола">
	<div id="wheel-image" style="display:none;">
		<div style="font-size:35px; margin-bottom:-9px; margin-left:183px;">&#9660;</div>
		<img width="400" height="400" src="wheel_of_fortune.png" />
		
		</div>
	
		<a href="#" id="save-link" style="display:none;">save</a>
	
	<script>
	var enableEdit = false;
var showMenu = true;
var isFirst = true;

$('#menu').click(function() {
    showMenu = !showMenu;
    $('#control-panel').css('display', (showMenu ? '' : 'none'));
});


$(document).ready(function() {

    $('#generate-crossword').click(function() {
        var x = parseInt($('#control-panel input[type="number"]').eq(0).val()),
            y = parseInt($('#control-panel input[type="number"]').eq(2).val());

        x = x > 30 ? 30 : (x < 5 ? 5 : x);
        y = y > 10 ? 10 : (y < 3 ? 3 : y);
        generateCrossword(x, y);

    });

	//Edit font-size
    document.querySelector('#font-size').addEventListener('input', function() {
        $('span').css('font-size', Number(this.value));
    });

	//Spinning wheel animation;
    $('img').click(function() {
        var prevStyle = this.style['-webkit-transform'],
            prevDeg = Number(prevStyle.substring(7, (prevStyle.lastIndexOf(')') - 3)));
        this.removeAttribute('style');

        var deg = 500 + Math.round(Math.random() * 499),
            easeOut = Math.round(deg / 100);
        deg += prevDeg;
        var css = '-webkit-transform: rotate(' + deg + 'deg); -webkit-transition: -webkit-transform ' + easeOut + 's ease-out;';

        this.setAttribute('style', css);
    });

    $('#save-template').click(function() {
        var rows = $('.row'),
            arr = [];
        for (var i = 0; i < rows.length; i++) {
            arr[i] = [];
            var letters = rows[i].querySelectorAll('span');

            for (var j = 0; j < letters.length; j++) {
                arr[i].push(letters[j].textContent);
            }
        };

        var blob = new Blob([JSON.stringify(arr)], {
                type: "application/json"
            }),
            url = URL.createObjectURL(blob),
            aLink = document.getElementById('save-link');

        aLink.download = 'crossword.txt';
        aLink.href = url;
        aLink.click();
    });

    document.getElementById('load-template').addEventListener('change', function(e) {
        var file = e.target.files[0],
            reader = new FileReader();
        reader.onload = function(event) {

            var contents = event.target.result,
				arr = JSON.parse(contents);
            generateCrossword(arr[0].length, arr.length, arr);
        };
        reader.readAsText(file);
    }, false);

});

var showWheel = false,
	showPass = false;
$('#wheel').click(function(){
	if($('#wheel-image').css('display') === 'block'){
		if(confirm('Hide wheel?')){
			$('#wheel-image').css('display','none');
			showWheel = false;
		}
		return;
	}
	showPass = !showPass;
	$('#pass-field').css('display', (showPass ? '' : 'none'));
});

$('#pass-field').on('keypress', function(ev){
	if($('#wheel-image').css('display') === 'block'){
		return;
	}
	var mins = new Date().getMinutes();
	if (ev.keyCode === 13 && this.value === 'nebarai' + mins){
		$('#wheel-image').css('display', '')
	}else if(ev.keyCode === 13){
		alert('Сори брат!');
	}
});

function generateCrossword(x, y, arr) {
    if ($('#crossword-wrapper')) {
        $('#crossword-wrapper').remove();
    }
	enableEdit = false;
    var $prevLetter = $();

    $(document.body).append('<div id="crossword-wrapper"></div>');

    $('#crossword-wrapper').append('<table id="crossword-puzzle"></table>');

    $('table').css('height', window.innerHeight);
    $('table').append(generateFirstRow(x));

    var indexer = 0,
		letters = 'АБВГДЕЖЗИЙКЛМНОПРСТУФХЦЧШЩЪьЮЯ';
        
    for (var a = 0; a < y; a++) {
        var row = $('<tr class="row">');

        for (var b = 0; b <= x; b++) {
            if (b === 0) {
                row.append('<td class="num-indexer">' + letters[indexer] + '</td>');
                indexer++;
            } else {
                var letter = arr ? arr[a][b-1] : '';
                row.append('<td><span>' + letter + '</span></td>');
            }
        }
        $('table').append(row);
    }
    $('td').css('width', ($('table').width() / $('tr:nth-child(1) td').length) - 30);
    $('.num-indexer').css('width', '2%');
    
	$('span').css('font-size', Number($('#font-size').val()));
	
	$('td').click(function(ev) {

        if (enableEdit || this.className.indexOf('indexer') != -1) {
            return;
        }

        if (this.className === 'selected-letter') {

            if (isFirst) {
				alert('Къци Ви пожелава успех !!!');
            }
            isFirst = false;
			
			if(this.textContent == '*'){
				alert('Бомба брат! Сядай си...');
				this.innerHTML = '&#128163;';
				this.style.color = 'red';
				this.style.fontSize = '46px';
			}
			
			if(this.textContent == '!'){
				alert('Виц! Виц! Виц!');
				this.innerHTML = '&#9786;';
				this.style.color = 'green';
				this.style.fontSize = '60px';
			}
		
            $(this).find('span').animate({
                'opacity': 1
            }, 1000);
            $(this).addClass('revealed-letter');
			
            $(this).removeClass('selected-letter');
            $prevLetter = $();

            return;
        } else if ($prevLetter.length !== 0) {
            $prevLetter.removeClass('selected-letter');
        }

        $(this).addClass('selected-letter');
        $prevLetter = $(this);
    });


    $('#edit-crossword').click(function() {
        if (!enableEdit) {
            $('td').attr('class', '');
            $('span').attr('class', 'edit-letter');
            $('.edit-letter').attr('contenteditable', 'true');
        }
        enableEdit = true;
        $('#clear-crossword').attr('disabled', false);
    });

    $('#clear-crossword').click(function() {
        $('span').html('');
    });
	
    $('#start-game').click(function() {
        enableEdit = false;
        $('span').attr('class', '');
        $('span').attr('contenteditable', 'false');
        $('#clear-crossword').attr('disabled', true);
    });

    $('.disabled').removeAttr('disabled');
    $('#clear-crossword').attr('disabled', true);
    setHeights();
}

function generateFirstRow(len) {
    var result = '<td class="indexer"></td>';
    for (var i = 1; i <= len; i++) {
        result += '<td class="indexer">' + i + '</td>';
    }
    return '<tr>' + result + '</tr>';
}

function setHeights() {
    var height = $('td').height();
    $('span').css('height', height);
}





	</script>
	</body>
</html>
