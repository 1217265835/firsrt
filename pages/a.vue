<template>
  <div class="page-title-area" id="xa">
    <div class="row align-items-center">
      <div class="col-sm-6 clearfix">
        <div class="user-profile pull-left">
          <img
            class="avatar user-thumb"
            src="../assets/images/author/avatar.png"
            alt="avatar"
          />
          <h4 class="user-name dropdown-toggle" data-toggle="dropdown">
            Kumkum Rai <i class="fa fa-angle-down"></i>
          </h4>
        </div>
      </div>
      <div class="col-sm-6">
        <ul class="notification-area pull-right">
          <li id="full-view"><i class="ti-fullscreen"></i></li>
          <li id="full-view-exit"><i class="ti-zoom-out"></i></li>
          <li class="dropdown">
            <i class="ti-bell dropdown-toggle" data-toggle="dropdown">
              <span>2</span>
            </i>
          </li>
          <li class="dropdown">
            <i class="fa fa-envelope-o dropdown-toggle" data-toggle="dropdown"
              ><span>3</span></i
            >
          </li>
          <li class="settings-btn">
            <i class="ti-settings"></i>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style>
@import "../assets/css/bootstrap.min.css";
@import "../assets/css/font-awesome.min.css";
@import "../assets/css/themify-icons.css";
@import "../assets/css/metisMenu.css";
@import "../assets/css/slicknav.min.css";
@import "../assets/css/typography.css";
@import "../assets/css/default-css.css";
@import "../assets/css/styles.css";
@import "../assets/css/responsive.css";
.interact:hover {
  background-color: #bdd7ee;
}
</style>

<script>
var sortnum=1;
var TopCanvasDiv = document.createElement("div");
TopCanvasDiv.style.left = (100 * 0) / 96 + "%";
TopCanvasDiv.style.top = (100 * 0) / 48 + "%";
TopCanvasDiv.style.width = (100 * 96) / 96 + "%";
TopCanvasDiv.style.height = (100 * 4) / 48 + "%";
TopCanvasDiv.style.position = "absolute";
document.body.appendChild(TopCanvasDiv);
var middiv = document.createElement("div");
middiv.style.left = (100 * 0) / 96 + "%";
middiv.style.top = (100 * 4) / 48 + "%";
middiv.style.width = (100 * 96) / 96 + "%";
middiv.style.height = (100 * 1.3) / 48 + "%";
middiv.style.position = "absolute";
document.body.appendChild(middiv);
middiv.style.backgroundColor = "#F2F2F2";
var sortDiv = document.createElement("div");
sortDiv.style.left = (100 * 76) / 96 + "%";
sortDiv.style.top = (100 * 4) / 48 + "%";
sortDiv.style.width = (100 * 16) / 96 + "%";
sortDiv.style.height = (100 * 1.3) / 48 + "%";
sortDiv.style.position = "absolute";
document.body.appendChild(sortDiv);
var sortDiv1 = document.createElement("div");
sortDiv1.style.left = (100 * 0) / 16 + "%";
sortDiv1.style.top = (100 * 0) / 1.3 + "%";
sortDiv1.style.width = (100 * 4) / 16 + "%";
sortDiv1.style.height = (100 * 1.3) / 1.3 + "%";
sortDiv1.style.position = "absolute";
sortDiv.appendChild(sortDiv1);
sortDiv1.innerText = "序号升序";
sortDiv1.style.textAlign = "center";
sortDiv1.style.fontSize = (((100 * 10) / 20) * 20) / 1920 + "vw";
sortDiv1.className = "interact";
var sortDiv2 = document.createElement("div");
sortDiv2.style.left = (100 * 4) / 16 + "%";
sortDiv2.style.top = (100 * 0) / 1.3 + "%";
sortDiv2.style.width = (100 * 4) / 16 + "%";
sortDiv2.style.height = (100 * 1.3) / 1.3 + "%";
sortDiv2.style.position = "absolute";
sortDiv.appendChild(sortDiv2);
sortDiv2.innerText = "序号降序";
sortDiv2.style.textAlign = "center";
sortDiv2.style.fontSize = (((100 * 10) / 20) * 20) / 1920 + "vw";
sortDiv2.className = "interact";
var sortDiv3 = document.createElement("div");
sortDiv3.style.left = (100 * 8) / 16 + "%";
sortDiv3.style.top = (100 * 0) / 1.3 + "%";
sortDiv3.style.width = (100 * 4) / 16 + "%";
sortDiv3.style.height = (100 * 1.3) / 1.3 + "%";
sortDiv3.style.position = "absolute";
sortDiv.appendChild(sortDiv3);
sortDiv3.innerText = "时间升序";
sortDiv3.style.textAlign = "center";
sortDiv3.style.fontSize = (((100 * 10) / 20) * 20) / 1920 + "vw";
sortDiv3.className = "interact";
var sortDiv4 = document.createElement("div");
sortDiv4.style.left = (100 * 12) / 16 + "%";
sortDiv4.style.top = (100 * 0) / 1.3 + "%";
sortDiv4.style.width = (100 * 4) / 16 + "%";
sortDiv4.style.height = (100 * 1.3) / 1.3 + "%";
sortDiv4.style.position = "absolute";
sortDiv.appendChild(sortDiv4);
sortDiv4.innerText = "时间降序";
sortDiv4.style.textAlign = "center";
sortDiv4.style.fontSize = (((100 * 10) / 20) * 20) / 1920 + "vw";
sortDiv4.className = "interact";
window.onload = function () {
  var settings = document.getElementById("xa");
  console.log(settings);
  TopCanvasDiv.appendChild(settings);
  sortDiv1.style.backgroundColor="#2f75b5";
  sortDiv1.style.color="#FFFFFF";
  sortnum=1;
};
sortDiv1.addEventListener('mouseup',function(e){
    sortDiv2.backgroundColor=;






});
</script>