# EducationforLife
Ejemplo#Ejemplo Practico
<div id="book">
  <canvas id="pageflip-canvas"></canvas>
  <div id="pages">
    <section>
      <div> <!-- Any type of contents here --> </div>
    </section>
    <!-- More <section>'s here -->
  </div>
</div>
var BOOK_WIDTH = 830;
var BOOK_HEIGHT = 260;
var PAGE_WIDTH = 400;
var PAGE_HEIGHT = 250;
var PAGE_Y = ( BOOK_HEIGHT - PAGE_HEIGHT ) / 2;
var CANVAS_PADDING = 60;
// Create a reference to the book container element
var book = document.getElementById( "book" );

// Grab a list of all section elements (pages) within the book
var pages = book.getElementsByTagName( "section" );

for( var i = 0, len = pages.length; i < len; i++ ) {
    pages[i].style.zIndex = len - i;

    flips.push( {
    progress: 1,
    target: 1,
    page: pages[i],
    dragging: false
  });
}
function mouseMoveHandler( event ) {
  // Offset mouse position so that the top of the book spine is 0,0
  mouse.x = event.clientX - book.offsetLeft - ( BOOK_WIDTH / 2 );
  mouse.y = event.clientY - book.offsetTop;
}

function mouseDownHandler( event ) {
  // Make sure the mouse pointer is inside of the book
  if (Math.abs(mouse.x) < PAGE_WIDTH) {
    if (mouse.x < 0 && page - 1 >= 0) {
      // We are on the left side, drag the previous page
      flips[page - 1].dragging = true;
    }
    else if (mouse.x > 0 && page + 1 < flips.length) {
      // We are on the right side, drag the current page
      flips[page].dragging = true;
    }
  }

  // Prevents the text selection
  event.preventDefault();
}

function mouseUpHandler( event ) {
  for( var i = 0; i < flips.length; i++ ) {
    // If this flip was being dragged, animate to its destination
    if( flips[i].dragging ) {
      // Figure out which page we should navigate to
      if( mouse.x < 0 ) {
        flips[i].target = -1;
        page = Math.min( page + 1, flips.length );
      }
      else {
        flips[i].target = 1;
        page = Math.max( page - 1, 0 );
      }
    }

    flips[i].dragging = false;
  }
}
function render() {
  // Reset all pixels in the canvas
  context.clearRect( 0, 0, canvas.width, canvas.height );

  for( var i = 0, len = flips.length; i < len; i++ ) {
    var flip = flips[i];

    if( flip.dragging ) {
      flip.target = Math.max( Math.min( mouse.x / PAGE_WIDTH, 1 ), -1 );
    }

    // Ease progress towards the target value
    flip.progress += ( flip.target - flip.progress ) * 0.2;

    // If the flip is being dragged or is somewhere in the middle
    // of the book, render it
    if( flip.dragging || Math.abs( flip.progress ) < 0.997 ) {
      drawFlip( flip );
    }

  }
// Determines the strength of the fold/bend on a 0-1 range
var strength = 1 - Math.abs( flip.progress );

// Width of the folded paper
var foldWidth = ( PAGE_WIDTH * 0.5 ) * ( 1 - flip.progress );

// X position of the folded paper
var foldX = PAGE_WIDTH * flip.progress + foldWidth;

// How far outside of the book the paper is bent due to perspective
var verticalOutdent = 20 * strength;

// The maximum widths of the three shadows used
var paperShadowWidth = (PAGE_WIDTH*0.5) * Math.max(Math.min(1 - flip.progress, 0.5), 0);
var rightShadowWidth = (PAGE_WIDTH*0.5) * Math.max(Math.min(strength, 0.5), 0);
var leftShadowWidth = (PAGE_WIDTH*0.5) * Math.max(Math.min(strength, 0.5), 0);

// Mask the page by setting its width to match the foldX
flip.page.style.width = Math.max(foldX, 0) + "px";
