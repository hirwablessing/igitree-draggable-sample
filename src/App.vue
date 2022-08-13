<template>
  <!-- mydiv for id is used for styling and the other one inside ref is used for the functionality -->
  <div id="mydiv" ref="myDiv">
    <div class="container">
      <div class="row">
        <div class="tree">
          <ul>
            <li>
              <a href="#"><img src="/images/1.jpg" /><span>Child</span></a>
              <ul>
                <li>
                  <a href="#"
                    ><img src="/images/2.jpg" /><span>Grand Child</span></a
                  >
                  <ul>
                    <li>
                      <a href="#"
                        ><img src="/images/3.jpg" /><span
                          >Great Grand Child</span
                        ></a
                      >
                    </li>
                    <li>
                      <a href="#"
                        ><img src="/images/4.jpg" /><span
                          >Great Grand Child</span
                        ></a
                      >
                    </li>
                  </ul>
                </li>
                <li>
                  <a href="#"
                    ><img src="/images/5.jpg" /><span>Grand Child</span></a
                  >
                  <ul>
                    <li>
                      <a href="#"
                        ><img src="/images/6.jpg" /><span
                          >Great Grand Child</span
                        ></a
                      >
                    </li>
                    <li>
                      <a href="#"
                        ><img src="/images/7.jpg" /><span
                          >Great Grand Child</span
                        ></a
                      >
                    </li>
                    <li>
                      <a href="#"
                        ><img src="/images/8.jpg" /><span
                          >Great Grand Child</span
                        ></a
                      >
                    </li>
                  </ul>
                </li>

                <li>
                  <a href="#"
                    ><img src="/images/9.jpg" /><span>Grand Child</span></a
                  >
                </li>
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

let pos1 = 0,
  pos2 = 0,
  pos3 = 0,
  pos4 = 0;

let scale = 1,
  panning = false,
  pointX = 0,
  pointY = 0,
  start = { x: 0, y: 0 };

const elmnt = ref(null);
const myDiv = ref(null);

// export default {
//   methods: {
const elementDrag = (e = window.event) => {
  e.preventDefault();
  // calculate the new cursor position:
  pos1 = pos3 - e.clientX;
  pos2 = pos4 - e.clientY;
  pos3 = e.clientX;
  pos4 = e.clientY;
  // set the element's new position:
  elmnt.value.style.top = elmnt.value.offsetTop - pos2 + "px";
  elmnt.value.style.left = elmnt.value.offsetLeft - pos1 + "px";
};

const closeDragElement = () => {
  /* stop moving when mouse button is released:*/
  document.onmouseup = null;
  document.onmousemove = null;
};

const dragElement = () => {
  // inside the real implementation we should also use refs instead of accessing the document directly
  if (document.getElementById(elmnt.value.id + "header")) {
    /* if present, the header is where you move the DIV from:*/
    document.getElementById(elmnt.value.id + "header").onmousedown =
      dragMouseDown;
  } else {
    /* otherwise, move the DIV from anywhere inside the DIV:*/
    elmnt.value.onmousedown = dragMouseDown;
  }
};

const dragMouseDown = (e = window.event) => {
  e.preventDefault();
  // get the mouse cursor position at startup:
  pos3 = e.clientX;
  pos4 = e.clientY;
  document.onmouseup = closeDragElement;
  // call a function whenever the cursor moves:
  document.onmousemove = elementDrag;
};

const setTransform = () => {
  elmnt.value.style.transform =
    "translate(" + pointX + "px, " + pointY + "px) scale(" + scale + ")";
};

const zoom = () => {
  (elmnt.value.onmousedown = (e) => {
    e.preventDefault();
    start = { x: e.clientX - pointX, y: e.clientY - pointY };
    panning = true;
  }),
    (elmnt.value.onmouseup = (e) => {
      panning = false;
    }),
    (elmnt.value.onmousemove = (e) => {
      e.preventDefault();
      if (!panning) {
        return;
      }
      pointX = e.clientX - start.x;
      pointY = e.clientY - start.y;
      setTransform();
    }),
    (elmnt.value.onwheel = (e) => {
      e.preventDefault();

      let xs = (e.clientX - pointX) / scale,
        ys = (e.clientY - pointY) / scale,
        delta = e.wheelDelta ? e.wheelDelta : -e.deltaY;

      delta > 0 ? (scale *= 1.1) : (scale /= 1.1);
      pointX = e.clientX - xs * scale;
      pointY = e.clientY - ys * scale;

      setTransform();
    });
};

onMounted(() => {
  elmnt.value = myDiv.value;
  dragElement();
  zoom();
});
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
* {
  padding: 0px;
  margin: 0px;
  box-sizing: border-box;
}
body {
  height: 100vh;
  display: grid;
  align-items: center;
  font-family: "Poppins", sans-serif;
}
.tree {
  width: 100%;
  height: auto;
  text-align: center;
}
.tree ul {
  padding-top: 20px;
  position: relative;
  transition: 0.5s;
}
.tree li {
  display: inline-table;
  text-align: center;
  list-style-type: none;
  position: relative;
  padding: 10px;
  transition: 0.5s;
}
.tree li::before,
.tree li::after {
  content: "";

  top: 0;
  right: 50%;
  border-top: 1px solid #ccc;
  width: 51%;
  height: 10px;
}
.tree li::after {
  right: auto;
  left: 50%;
  border-left: 1px solid #ccc;
}
.tree li:only-child::after,
.tree li:only-child::before {
  display: none;
}
.tree li:only-child {
  padding-top: 0;
}
.tree li:first-child::before,
.tree li:last-child::after {
  border: 0 none;
}
.tree li:last-child::before {
  border-right: 1px solid #ccc;
  border-radius: 0 5px 0 0;
  -webkit-border-radius: 0 5px 0 0;
  -moz-border-radius: 0 5px 0 0;
}
.tree li:first-child::after {
  border-radius: 5px 0 0 0;
  -webkit-border-radius: 5px 0 0 0;
  -moz-border-radius: 5px 0 0 0;
}
.tree ul ul::before {
  content: "";
  position: absolute;
  top: 0;
  left: 50%;
  border-left: 1px solid #ccc;
  width: 0;
  height: 20px;
}
.tree li a {
  border: 1px solid #ccc;
  padding: 10px;
  display: inline-grid;
  border-radius: 5px;
  text-decoration-line: none;
  border-radius: 5px;
  transition: 0.5s;
}
.tree li a img {
  width: 50px;
  height: 50px;
  margin-bottom: 10px !important;
  border-radius: 100px;
  margin: auto;
}
.tree li a span {
  border: 1px solid #ccc;
  border-radius: 5px;
  color: #666;
  padding: 8px;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 500;
}
/*Hover-Section*/
.tree li a:hover,
.tree li a:hover i,
.tree li a:hover span,
.tree li a:hover + ul li a {
  background: #c8e4f8;
  color: #000;
  border: 1px solid #94a0b4;
}
.tree li a:hover + ul li::after,
.tree li a:hover + ul li::before,
.tree li a:hover + ul::before,
.tree li a:hover + ul ul::before {
  border-color: #94a0b4;
}

#mydiv {
  position: absolute;
  z-index: 9;
  background-color: #f1f1f1;
  text-align: center;
  border: 1px solid #d3d3d3;
  overflow-x: scroll;
}

#mydivheader {
  padding: 10px;
  cursor: move;
  z-index: 10;
  background-color: #2196f3;
  color: #fff;
}
</style>
