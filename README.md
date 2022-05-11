# EducationforLife
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
