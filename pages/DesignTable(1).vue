<template>
  <body>
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
  </body>
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
.interact2 {
  background-color: #bfbfbf;
}
.interact2:hover {
  background-color: #2f75b5;
}
</style>
<script>
var ElementArray = new Array();
var BaseDiv = null,
  BaseCanvas = null;
var InterAct = {
  ChosenDivId: null,
  MouseOffsetX: null,
  MouseOffsetY: null,
  ActMode: null,
};
var LastEditId = null;
const CSTRING = "0123456789ABCDEF";
const SERVER_URL='127.0.0.1:3000';
var P_Name = "未命名的设计",
  P_Id = -1,
  U_Id = -1,
  P_Description = "无描述",
  C_Time = "00-00-00";
var xhr=null;
var BaseCanvasInput = null;
//var BaseCanvasInput = querySelector('#imginput');
var BaseImg = new Image();

function ToJson() {
  let res = new Array();
  let BaseWidth = parseFloat(BaseDiv.style.width),
    BaseHeight = parseFloat(BaseDiv.style.height);
  for (let i = 0; i < ElementArray.length; i++) {
    let tobj = {
      PosX: parseInt(ElementArray[i].FatherDiv.style.left) / BaseWidth,
      PosY: parseInt(ElementArray[i].FatherDiv.style.top) / BaseHeight,
      Id: i,
      Height: parseInt(ElementArray[i].FatherDiv.style.height) / BaseHeight,
      Width: parseInt(ElementArray[i].FatherDiv.style.width) / BaseWidth,
      Color: ElementArray[i].MainColor,
      Text: ElementArray[i].TextDiv.innerText,
      FontSize: ElementArray[i].TextDiv.style.fontSize.slice(0, -2),
      Type: ElementArray[i].ElementType,
      TextAlign: ElementArray[i].TextDiv.style.textAlign,
    };
    res[i] = tobj;
  }
  let bodyres = {
    ProjectId: P_Id,
    UserId: U_Id,
    ProjectName: P_Name,
    CreateTime: C_Time,
    Description: P_Description,
    ProjectWidth: parseInt(BaseDiv.style.width),
    ProjectHeight: parseInt(BaseDiv.style.height),
    ProjectCover: BaseCanvas.toDataURL("image/png"),
    ElementArray: res,
  };

  let jres = JSON.stringify(bodyres);
  return jres;
  console.log(jres);
}
function DrawByJson(jstring) {
  let jsonobj = JSON.parse(jstring);
  //let offsetindex=ElementArray.length;
  for (let i = 0; i < jsonobj.length; i++) {
    NewElement(0, jsonobj[i]);
  }
  //console.log("readdone");
}
function Init() {
  BaseDiv = document.createElement("div");
  BaseCanvas = document.createElement("canvas");
  BaseDiv.style.outline = "none";
  //BaseDiv.style.border='3px #000000 solid';
  BaseDiv.style.left = (100 * 21) / 96 + "%";
  BaseDiv.style.top = (100 * 1) / 48 + "%";
  BaseDiv.style.width = "1460px";
  BaseDiv.style.height = "821px";
  BaseDiv.style.backgroundColor = "#FFFFFF";
  BaseDiv.style.position = "absolute";
  BaseCanvas.width = 1460;
  BaseCanvas.height = 821;
  BaseCanvas.style.left = "0px";
  BaseCanvas.style.top = "0px";
  var CanvasBottomDiv = document.createElement("div");
  CanvasBottomDiv.style.left = (100 * 0) / 96 + "%";
  CanvasBottomDiv.style.top = (100 * 5) / 48 + "%";
  CanvasBottomDiv.style.width = (100 * 96) / 96 + "%";
  CanvasBottomDiv.style.height = (100 * 43) / 48 + "%";
  CanvasBottomDiv.style.position = "absolute";
  CanvasBottomDiv.style.backgroundColor = "#F2F2F2";
  document.body.appendChild(CanvasBottomDiv);
  BaseDiv.addEventListener("mousedown", function (e) {
    for (let i = 0; i < ElementArray.length; i++) {
      if (i != InterAct.ChosenDivId) {
        ElementArray[i].TextDiv.contentEditable = false;
        ElementArray[i].ShapeDiv.style.display = "none";
        ElementArray[i].CanvasDiv.style.border = "";
      } else {
        ElementArray[i].TextDiv.contentEditable = true;
        ElementArray[i].ShapeDiv.style.display = "";
        ElementArray[i].CanvasDiv.style.border =
          "2px " + ElementArray[i].MainColor + " solid";
      }
    }
  });
  BaseDiv.addEventListener("mousemove", function (e) {
    if (InterAct.ChosenDivId != null) {
      let mx = e.clientX,
        my = e.clientY;
      if (mx < BaseDiv.getBoundingClientRect().left)
        mx = BaseDiv.getBoundingClientRect().left;
      if (mx > BaseDiv.getBoundingClientRect().right)
        mx = BaseDiv.getBoundingClientRect().right;
      if (my < BaseDiv.getBoundingClientRect().top)
        my = BaseDiv.getBoundingClientRect().top;
      if (my > BaseDiv.getBoundingClientRect().bottom)
        my = BaseDiv.getBoundingClientRect().bottom;
      ElementArray[InterAct.ChosenDivId].TextDiv.contentEditable = true;
      ElementArray[InterAct.ChosenDivId].ShapeDiv.style.display = "";
      ElementArray[InterAct.ChosenDivId].CanvasDiv.style.border =
        "2px " + ElementArray[InterAct.ChosenDivId].MainColor + " solid";
      if (InterAct.ActMode == 1) {
        ElementArray[InterAct.ChosenDivId].FatherDiv.style.left =
          mx - InterAct.MouseOffsetX + "px";
        ElementArray[InterAct.ChosenDivId].FatherDiv.style.top =
          my - InterAct.MouseOffsetY + "px";
      } else if (InterAct.ActMode == 2) {
        ElementArray[InterAct.ChosenDivId].ShapeDiv.style.left =
          mx - InterAct.MouseOffsetX + "px";
        ElementArray[InterAct.ChosenDivId].ShapeDiv.style.top =
          my - InterAct.MouseOffsetY + "px";

        ElementArray[InterAct.ChosenDivId].TextDiv.style.width =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.left.slice(0, -2)
          ) + "px";
        ElementArray[InterAct.ChosenDivId].TextDiv.style.height =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.top.slice(0, -2)
          ) + "px";

        ElementArray[InterAct.ChosenDivId].CanvasDiv.style.width =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.left.slice(0, -2)
          ) + "px";
        ElementArray[InterAct.ChosenDivId].CanvasDiv.style.height =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.top.slice(0, -2)
          ) + "px";

        ElementArray[InterAct.ChosenDivId].Canvas.width = Math.max(
          1,
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.left.slice(0, -2)
          )
        );
        ElementArray[InterAct.ChosenDivId].Canvas.height = Math.max(
          1,
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.top.slice(0, -2)
          )
        );

        ElementArray[InterAct.ChosenDivId].FatherDiv.style.width =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.left.slice(0, -2)
          ) +
          ElementArray[InterAct.ChosenDivId].ShapeDivSize +
          "px";
        ElementArray[InterAct.ChosenDivId].FatherDiv.style.height =
          parseInt(
            ElementArray[InterAct.ChosenDivId].ShapeDiv.style.top.slice(0, -2)
          ) +
          ElementArray[InterAct.ChosenDivId].ShapeDivSize +
          "px";
      }
    }
  });
  document.addEventListener("mouseup", function (e) {
    if (InterAct.ChosenDivId != null && InterAct.ActMode == 2) {
      let ctx = ElementArray[InterAct.ChosenDivId].Canvas.getContext("2d");
      let type = ElementArray[InterAct.ChosenDivId].ElementType;
      ctx.fillStyle = ctx.strokeStyle =
        ElementArray[InterAct.ChosenDivId].MainColor;
      if (type == 0) {
        Draw_Rect_In_Rect(
          ctx,
          ElementArray[InterAct.ChosenDivId].Canvas.width,
          ElementArray[InterAct.ChosenDivId].Canvas.height
        );
      } else if (type == 1) {
        Draw_Delta_In_Rect(
          ctx,
          ElementArray[InterAct.ChosenDivId].Canvas.width,
          ElementArray[InterAct.ChosenDivId].Canvas.height
        );
      } else if (type == 2) {
        Draw_Star_In_Rect(
          ctx,
          ElementArray[InterAct.ChosenDivId].Canvas.width,
          ElementArray[InterAct.ChosenDivId].Canvas.height
        );
      } else if (type == 3) {
        Draw_Arc_In_Rect(
          ctx,
          ElementArray[InterAct.ChosenDivId].Canvas.width,
          ElementArray[InterAct.ChosenDivId].Canvas.height
        );
      }
    }
    InterAct = {
      ChosenDivId: null,
      MouseOffsetX: null,
      MouseOffsetY: null,
      ActMode: null,
    };
  });
  CanvasBottomDiv.appendChild(BaseDiv);
  BaseDiv.appendChild(BaseCanvas);

  var InformationBottomDiv = document.createElement("div");
  InformationBottomDiv.style.left = (100 * 0) / 96 + "%";
  InformationBottomDiv.style.top = (100 * 4) / 48 + "%";
  InformationBottomDiv.style.width = (100 * 96) / 96 + "%";
  InformationBottomDiv.style.height = (100 * 1.5) / 48 + "%";
  InformationBottomDiv.style.position = "absolute";
  InformationBottomDiv.style.backgroundColor = "#DDDDDD";
  document.body.appendChild(InformationBottomDiv);

  var BackDiv = document.createElement("div");
  BackDiv.style.left = (100 * 0) / 96 + "%";
  BackDiv.style.top = (100 * 0) / 1.5 + "%";
  BackDiv.style.width = (100 * 6) / 96 + "%";
  BackDiv.style.height = (100 * 1.5) / 1.5 + "%";
  BackDiv.style.position = "absolute";
  BackDiv.className = "interact";
  BackDiv.style.fontSize = (((100 * 20) / 20) * 20) / 1920 + "vw";
  BackDiv.innerText = "<返回首页";
  InformationBottomDiv.appendChild(BackDiv);

  var SizeAndTimeDiv = document.createElement("div");
  SizeAndTimeDiv.style.left = BaseDiv.style.left;
  SizeAndTimeDiv.style.top = (100 * 0.7) / 1.5 + "%";
  SizeAndTimeDiv.style.width = (100 * 27) / 96 + "%";
  SizeAndTimeDiv.style.height = (100 * 0.8) / 1.5 + "%";
  SizeAndTimeDiv.style.fontSize = (((100 * 12) / 20) * 20) / 1920 + "vw";
  SizeAndTimeDiv.style.position = "absolute";
  SizeAndTimeDiv.innerText =
    "画布尺寸:" +
    BaseDiv.style.width.slice(0, -2) +
    "x" +
    BaseDiv.style.height.slice(0, -2) +
    "，创建时间:" +
    C_Time;
  InformationBottomDiv.appendChild(SizeAndTimeDiv);

  var ProjectNameDiv = document.createElement("div");
  InformationBottomDiv.appendChild(ProjectNameDiv);
  ProjectNameDiv.style.width = (100 * 21) / 96 + "%";
  ProjectNameDiv.style.height = (100 * 1.2) / 1.5 + "%";
  ProjectNameDiv.style.top = (100 * 0.3) / 1.5 + "%";
  ProjectNameDiv.style.left =
    BaseDiv.getBoundingClientRect().right -
    ProjectNameDiv.getBoundingClientRect().width +
    "px";
  ProjectNameDiv.style.fontSize = (((100 * 18) / 20) * 20) / 1920 + "vw";
  ProjectNameDiv.style.textAlign = "right";
  ProjectNameDiv.style.position = "absolute";
  ProjectNameDiv.innerText = P_Name;

  var InputBaseShape = document.createElement("div");
  InputBaseShape.style.left = (100 * 2) / 96 + "%";
  InputBaseShape.style.top = (100 * 0.5) / 43 + "%";
  InputBaseShape.style.width = (100 * 17) / 96 + "%";
  InputBaseShape.style.height = (100 * 1.5) / 43 + "%";
  InputBaseShape.style.fontSize = (((100 * 18) / 20) * 20) / 1920 + "vw";
  InputBaseShape.style["border-bottom"] = "2px solid #000000";
  InputBaseShape.innerText = "插入基本图形";
  InputBaseShape.style.position = "absolute";
  CanvasBottomDiv.appendChild(InputBaseShape);

  var FontChange = document.createElement("div");
  FontChange.style.left = (100 * 2) / 96 + "%";
  FontChange.style.top = (100 * 25.5) / 43 + "%";
  FontChange.style.width = (100 * 17) / 96 + "%";
  FontChange.style.height = (100 * 1.5) / 43 + "%";
  FontChange.style.fontSize = (((100 * 18) / 20) * 20) / 1920 + "vw";
  FontChange.style["border-bottom"] = "2px solid #000000";
  FontChange.innerText = "字体样式修改";
  FontChange.style.position = "absolute";
  CanvasBottomDiv.appendChild(FontChange);

  var OtherFunction = document.createElement("div");
  OtherFunction.style.left = (100 * 2) / 96 + "%";
  OtherFunction.style.top = (100 * 31.5) / 43 + "%";
  OtherFunction.style.width = (100 * 17) / 96 + "%";
  OtherFunction.style.height = (100 * 1.5) / 43 + "%";
  OtherFunction.style.fontSize = (((100 * 18) / 20) * 20) / 1920 + "vw";
  OtherFunction.style["border-bottom"] = "2px solid #000000";
  OtherFunction.innerText = "其他功能";
  OtherFunction.style.position = "absolute";
  CanvasBottomDiv.appendChild(OtherFunction);

  var InputRect = document.createElement("div");
  InputRect.style.left = (100 * 2) / 96 + "%";
  InputRect.style.top = (100 * 3) / 43 + "%";
  InputRect.style.width = (100 * 8) / 96 + "%";
  InputRect.style.height = (100 * 10) / 43 + "%";
  InputRect.style.position = "absolute";
  InputRect.className = "interact";
  CanvasBottomDiv.appendChild(InputRect);
  InputRect.addEventListener("mouseup", function (e) {
    NewElement(0);
  });

  var RectCanvas = document.createElement("canvas");
  RectCanvas.style.left = (100 * 0) / 8 + "%";
  RectCanvas.style.top = (100 * 0) / 10 + "%";
  RectCanvas.style.width = (100 * 8) / 8 + "%";
  RectCanvas.style.height = (100 * 8) / 10 + "%";
  RectCanvas.getContext("2d").strokeStyle = "#41719C";
  RectCanvas.getContext("2d").lineWidth = 2;
  Draw_Rect_In_Rect_Line(
    RectCanvas.getContext("2d"),
    RectCanvas.width,
    RectCanvas.height
  );
  InputRect.appendChild(RectCanvas);

  var TextRect = document.createElement("div");
  TextRect.style.left = (100 * 0) / 8 + "%";
  TextRect.style.top = (100 * 8.5) / 10 + "%";
  TextRect.style.width = (100 * 8) / 8 + "%";
  TextRect.style.height = (100 * 1.5) / 10 + "%";
  TextRect.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  TextRect.style.textAlign = "center";
  TextRect.innerText = "矩形";
  TextRect.style.position = "absolute";
  InputRect.appendChild(TextRect);

  var InputDelta = document.createElement("div");
  InputDelta.style.left = (100 * 11) / 96 + "%";
  InputDelta.style.top = (100 * 3) / 43 + "%";
  InputDelta.style.width = (100 * 8) / 96 + "%";
  InputDelta.style.height = (100 * 10) / 43 + "%";
  InputDelta.style.position = "absolute";
  InputDelta.className = "interact";
  CanvasBottomDiv.appendChild(InputDelta);
  InputDelta.addEventListener("mouseup", function (e) {
    NewElement(1);
  });

  var DeltaCanvas = document.createElement("canvas");
  DeltaCanvas.style.left = (100 * 0) / 8 + "%";
  DeltaCanvas.style.top = (100 * 0) / 10 + "%";
  DeltaCanvas.style.width = (100 * 8) / 8 + "%";
  DeltaCanvas.style.height = (100 * 8) / 10 + "%";
  DeltaCanvas.getContext("2d").strokeStyle = "#41719C";
  DeltaCanvas.getContext("2d").lineWidth = 2;
  Draw_Delta_In_Rect_Line(
    DeltaCanvas.getContext("2d"),
    DeltaCanvas.width,
    DeltaCanvas.height
  );
  InputDelta.appendChild(DeltaCanvas);

  var TextDelta = document.createElement("div");
  TextDelta.style.left = (100 * 0) / 8 + "%";
  TextDelta.style.top = (100 * 8.5) / 10 + "%";
  TextDelta.style.width = (100 * 8) / 8 + "%";
  TextDelta.style.height = (100 * 1.5) / 10 + "%";
  TextDelta.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  TextDelta.style.textAlign = "center";
  TextDelta.innerText = "三角形";
  TextDelta.style.position = "absolute";
  InputDelta.appendChild(TextDelta);

  var InputStar = document.createElement("div");
  InputStar.style.left = (100 * 2) / 96 + "%";
  InputStar.style.top = (100 * 14) / 43 + "%";
  InputStar.style.width = (100 * 8) / 96 + "%";
  InputStar.style.height = (100 * 10) / 43 + "%";
  InputStar.style.position = "absolute";
  InputStar.className = "interact";
  CanvasBottomDiv.appendChild(InputStar);
  InputStar.addEventListener("mouseup", function (e) {
    NewElement(2);
  });

  var StarCanvas = document.createElement("canvas");
  StarCanvas.style.left = (100 * 0) / 8 + "%";
  StarCanvas.style.top = (100 * 0) / 10 + "%";
  StarCanvas.style.width = (100 * 8) / 8 + "%";
  StarCanvas.style.height = (100 * 8) / 10 + "%";
  StarCanvas.getContext("2d").strokeStyle = "#41719C";
  StarCanvas.getContext("2d").lineWidth = 2;
  Draw_Star_In_Rect_Line(
    StarCanvas.getContext("2d"),
    StarCanvas.width,
    StarCanvas.height
  );
  InputStar.appendChild(StarCanvas);

  var TextStar = document.createElement("div");
  TextStar.style.left = (100 * 0) / 8 + "%";
  TextStar.style.top = (100 * 8.5) / 10 + "%";
  TextStar.style.width = (100 * 8) / 8 + "%";
  TextStar.style.height = (100 * 1.5) / 10 + "%";
  TextStar.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  TextStar.style.textAlign = "center";
  TextStar.innerText = "五角星";
  TextStar.style.position = "absolute";
  InputStar.appendChild(TextStar);

  var InputArc = document.createElement("div");
  InputArc.style.left = (100 * 11) / 96 + "%";
  InputArc.style.top = (100 * 14) / 43 + "%";
  InputArc.style.width = (100 * 8) / 96 + "%";
  InputArc.style.height = (100 * 10) / 43 + "%";
  InputArc.style.position = "absolute";
  InputArc.className = "interact";
  CanvasBottomDiv.appendChild(InputArc);
  InputArc.addEventListener("mouseup", function (e) {
    NewElement(3);
  });

  var ArcCanvas = document.createElement("canvas");
  ArcCanvas.style.left = (100 * 0) / 8 + "%";
  ArcCanvas.style.top = (100 * 0) / 10 + "%";
  ArcCanvas.style.width = (100 * 8) / 8 + "%";
  ArcCanvas.style.height = (100 * 8) / 10 + "%";
  ArcCanvas.getContext("2d").strokeStyle = "#41719C";
  ArcCanvas.getContext("2d").liArcneWidth = 2;
  Draw_Arc_In_Rect_Line(
    ArcCanvas.getContext("2d"),
    ArcCanvas.width,
    ArcCanvas.height
  );
  InputArc.appendChild(ArcCanvas);

  var TextArc = document.createElement("div");
  TextArc.style.left = (100 * 0) / 8 + "%";
  TextArc.style.top = (100 * 8.5) / 10 + "%";
  TextArc.style.width = (100 * 8) / 8 + "%";
  TextArc.style.height = (100 * 1.5) / 10 + "%";
  TextArc.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  TextArc.style.textAlign = "center";
  TextArc.innerText = "圆形";
  TextArc.style.position = "absolute";
  InputArc.appendChild(TextArc);

  var ChangeAlign = document.createElement("div");
  ChangeAlign.style.left = (100 * 2) / 96 + "%";
  ChangeAlign.style.top = (100 * 28) / 43 + "%";
  ChangeAlign.style.width = (100 * 8) / 96 + "%";
  ChangeAlign.style.height = (100 * 2) / 43 + "%";
  ChangeAlign.style.position = "absolute";
  ChangeAlign.className = "interact";
  CanvasBottomDiv.appendChild(ChangeAlign);
  ChangeAlign.addEventListener("mouseup", function (e) {
    AlignChange();
  });

  var TextChangeAlign = document.createElement("div");
  TextChangeAlign.style.left = (100 * 0) / 8 + "%";
  TextChangeAlign.style.top = (100 * 0.5) / 2 + "%";
  TextChangeAlign.style.width = (100 * 8) / 8 + "%";
  TextChangeAlign.style.height = (100 * 1.5) / 2 + "%";
  TextChangeAlign.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  TextChangeAlign.innerText = "修改对齐样式";
  TextChangeAlign.style.textAlign = "center";
  TextChangeAlign.style.position = "absolute";
  ChangeAlign.appendChild(TextChangeAlign);

  var FontSizeAdd = document.createElement("div");
  FontSizeAdd.style.left = (100 * 11) / 96 + "%";
  FontSizeAdd.style.top = (100 * 28) / 43 + "%";
  FontSizeAdd.style.width = (100 * 4) / 96 + "%";
  FontSizeAdd.style.height = (100 * 2) / 43 + "%";
  FontSizeAdd.style.position = "absolute";
  FontSizeAdd.className = "interact";
  CanvasBottomDiv.appendChild(FontSizeAdd);
  FontSizeAdd.addEventListener("mouseup", function (e) {
    FontSizeAddV(2);
  });

  var FontSizeAddText = document.createElement("div");
  FontSizeAddText.style.left = (100 * 0) / 4 + "%";
  FontSizeAddText.style.top = (100 * 0.5) / 2 + "%";
  FontSizeAddText.style.width = (100 * 4) / 4 + "%";
  FontSizeAddText.style.height = (100 * 1.5) / 2 + "%";
  FontSizeAddText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  FontSizeAddText.innerText = "增加字号";
  FontSizeAddText.style.textAlign = "center";
  FontSizeAddText.style.position = "absolute";
  FontSizeAdd.appendChild(FontSizeAddText);

  var FontSizeSub = document.createElement("div");
  FontSizeSub.style.left = (100 * 15) / 96 + "%";
  FontSizeSub.style.top = (100 * 28) / 43 + "%";
  FontSizeSub.style.width = (100 * 4) / 96 + "%";
  FontSizeSub.style.height = (100 * 2) / 43 + "%";
  FontSizeSub.style.position = "absolute";
  FontSizeSub.className = "interact";
  CanvasBottomDiv.appendChild(FontSizeSub);
  FontSizeSub.addEventListener("mouseup", function (e) {
    FontSizeAddV(-2);
  });

  var FontSizeSubText = document.createElement("div");
  FontSizeSubText.style.left = (100 * 0) / 4 + "%";
  FontSizeSubText.style.top = (100 * 0.5) / 2 + "%";
  FontSizeSubText.style.width = (100 * 4) / 4 + "%";
  FontSizeSubText.style.height = (100 * 1.5) / 2 + "%";
  FontSizeSubText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  FontSizeSubText.innerText = "减小字号";
  FontSizeSubText.style.textAlign = "center";
  FontSizeSubText.style.position = "absolute";
  FontSizeSub.appendChild(FontSizeSubText);

  var ToTop = document.createElement("div");
  ToTop.style.left = (100 * 2) / 96 + "%";
  ToTop.style.top = (100 * 34) / 43 + "%";
  ToTop.style.width = (100 * 8) / 96 + "%";
  ToTop.style.height = (100 * 2) / 43 + "%";
  ToTop.style.position = "absolute";
  ToTop.className = "interact";
  CanvasBottomDiv.appendChild(ToTop);
  ToTop.addEventListener("mouseup", function (e) {
    MoveToTop();
  });

  var ToTopText = document.createElement("div");
  ToTopText.style.left = (100 * 0) / 8 + "%";
  ToTopText.style.top = (100 * 0.5) / 2 + "%";
  ToTopText.style.width = (100 * 8) / 8 + "%";
  ToTopText.style.height = (100 * 1.5) / 2 + "%";
  ToTopText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  ToTopText.innerText = "置于顶层";
  ToTopText.style.textAlign = "center";
  ToTopText.style.position = "absolute";
  ToTop.appendChild(ToTopText);

  var DeleteElement = document.createElement("div");
  DeleteElement.style.left = (100 * 11) / 96 + "%";
  DeleteElement.style.top = (100 * 34) / 43 + "%";
  DeleteElement.style.width = (100 * 8) / 96 + "%";
  DeleteElement.style.height = (100 * 2) / 43 + "%";
  DeleteElement.style.position = "absolute";
  DeleteElement.className = "interact";
  CanvasBottomDiv.appendChild(DeleteElement);
  DeleteElement.addEventListener("mouseup", function (e) {
    RemoveElement();
  });

  var DeleteElementText = document.createElement("div");
  DeleteElementText.style.left = (100 * 0) / 8 + "%";
  DeleteElementText.style.top = (100 * 0.5) / 2 + "%";
  DeleteElementText.style.width = (100 * 8) / 8 + "%";
  DeleteElementText.style.height = (100 * 1.5) / 2 + "%";
  DeleteElementText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  DeleteElementText.innerText = "删除元素";
  DeleteElementText.style.textAlign = "center";
  DeleteElementText.style.position = "absolute";
  DeleteElement.appendChild(DeleteElementText);

  var SetBackground = document.createElement("div");
  SetBackground.style.left = (100 * 2) / 96 + "%";
  SetBackground.style.top = (100 * 37) / 43 + "%";
  SetBackground.style.width = (100 * 17) / 96 + "%";
  SetBackground.style.height = (100 * 2) / 43 + "%";
  SetBackground.style.position = "absolute";
  SetBackground.className = "interact";
  CanvasBottomDiv.appendChild(SetBackground);

  var SetBackgroundInput = document.createElement("input");
  SetBackgroundInput.id = "imginput";
  SetBackgroundInput.type = "file";
  SetBackgroundInput.style.left = (100 * 2) / 17 + "%";
  SetBackgroundInput.style.top = (100 * 1.1) / 2 + "%";
  SetBackgroundInput.style.width = (100 * 13) / 17 + "%";
  SetBackgroundInput.style.height = (100 * 0.9) / 2 + "%";
  SetBackgroundInput.style.position = "absolute";
  SetBackground.appendChild(SetBackgroundInput);

  var SetBackgroundText = document.createElement("div");
  SetBackgroundText.style.left = (100 * 0) / 17 + "%";
  SetBackgroundText.style.top = (100 * 0.1) / 2 + "%";
  SetBackgroundText.style.width = (100 * 17) / 17 + "%";
  SetBackgroundText.style.height = (100 * 1.1) / 2 + "%";
  SetBackgroundText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  SetBackgroundText.innerText = "设置背景图片";
  SetBackgroundText.style.textAlign = "center";
  SetBackgroundText.style.position = "absolute";
  SetBackground.appendChild(SetBackgroundText);

  var SaveProject = document.createElement("div");
  SaveProject.style.left = (100 * 2) / 96 + "%";
  SaveProject.style.top = (100 * 40) / 43 + "%";
  SaveProject.style.width = (100 * 17) / 96 + "%";
  SaveProject.style.height = (100 * 3) / 43 + "%";
  SaveProject.style.position = "absolute";
  //SaveProject.style.backgroundColor="#BFBFBF";
  SaveProject.className = "interact2";
  CanvasBottomDiv.appendChild(SaveProject);
  SaveProject.addEventListener("mouseup", function (e) {
    SaveToServer();
  });

  var SaveProjectText = document.createElement("div");
  SaveProjectText.style.left = (100 * 0) / 17 + "%";
  SaveProjectText.style.top = (100 * 1) / 3 + "%";
  SaveProjectText.style.width = (100 * 17) / 17 + "%";
  SaveProjectText.style.height = (100 * 2) / 3 + "%";
  SaveProjectText.style.fontSize = (((100 * 14) / 20) * 20) / 1920 + "vw";
  SaveProjectText.innerText = "保存项目";
  SaveProjectText.style.textAlign = "center";
  SaveProjectText.style.position = "absolute";
  SaveProject.appendChild(SaveProjectText);

  ReadFromServer();
}
function ColorRand() {
  let res = "#";
  for (let i = 0; i < 6; i++) {
    res += CSTRING[Math.floor(Math.random() * 16)];
  }
  return res;
}
function ReverseColor(rawcolor) {
  let res = "#";
  for (let i = 1; i <= 6; i++) {
    if (rawcolor[i] == "0") res += CSTRING[15];
    if (rawcolor[i] == "1") res += CSTRING[14];
    if (rawcolor[i] == "2") res += CSTRING[13];
    if (rawcolor[i] == "3") res += CSTRING[12];

    if (rawcolor[i] == "4") res += CSTRING[11];
    if (rawcolor[i] == "5") res += CSTRING[10];
    if (rawcolor[i] == "6") res += CSTRING[9];
    if (rawcolor[i] == "7") res += CSTRING[8];

    if (rawcolor[i] == "8") res += CSTRING[7];
    if (rawcolor[i] == "9") res += CSTRING[6];
    if (rawcolor[i] == "A") res += CSTRING[5];
    if (rawcolor[i] == "B") res += CSTRING[4];

    if (rawcolor[i] == "C") res += CSTRING[3];
    if (rawcolor[i] == "D") res += CSTRING[2];
    if (rawcolor[i] == "E") res += CSTRING[1];
    if (rawcolor[i] == "F") res += CSTRING[0];
  }
  return res;
}
function drawdelta(c, width, height, offsetx, offsety) {
  c.moveTo(width / 2, 0);
  c.lineTo(0, height);
  c.lineTo(width, height);
  c.fill();
}
function drawellipsetwo(context, x, y, a, b) {
  context.save();
  var r = a > b ? a : b;
  var ratioX = a / r;
  var ratioY = b / r;
  context.scale(ratioX, ratioY);
  context.beginPath();
  context.arc(x / ratioX, y / ratioY, r, 0, 2 * Math.PI, false);
  context.closePath();
  context.restore();
}
function Draw_Delta_In_Rect_Line(c, w, h) {
  c.moveTo(w / 2, 0);
  c.lineTo(0, h);
  c.lineTo(w, h);
  c.lineTo(w / 2, 0);
  c.stroke();
}
function Draw_Rect_In_Rect_Line(c, w, h) {
  c.rect(0, 0, w, h);
  c.stroke();
}
function Draw_Arc_In_Rect_Line(c, w, h) {
  drawellipsetwo(c, w / 2, h / 2, w / 2, h / 2);
  //ellipse(0, h/2.0,w/2.0,h/2.0,0,0,2*Math.PI);
  c.stroke();
}
function Draw_Star_In_Rect_Line(c, w, h) {
  let insidep = new Array(),
    outsidep = new Array();
  let raw_width = 1902.113032,
    raw_height = 1809.016994,
    raw_centerx = 951.056516,
    raw_centery = 1000;
  let centerx = (raw_centerx * w) / raw_width,
    centery = (raw_centery * h) / raw_height;
  for (let i = 0; i < 5; i++) {
    insidep[i] = {
      x: Math.cos(((54 + i * 72) / 180) * Math.PI) * 500,
      y: -Math.sin(((54 + i * 72) / 180) * Math.PI) * 500,
    };

    outsidep[i] = {
      x: Math.cos(((18 + i * 72) / 180) * Math.PI) * 1000,
      y: -Math.sin(((18 + i * 72) / 180) * Math.PI) * 1000,
    };
    insidep[i].x *= w / raw_width;
    insidep[i].y *= h / raw_height;
    outsidep[i].x *= w / raw_width;
    outsidep[i].y *= h / raw_height;
    insidep[i].x += centerx;
    insidep[i].y += centery;
    outsidep[i].x += centerx;
    outsidep[i].y += centery;
  }
  c.beginPath();
  c.moveTo(outsidep[0].x, outsidep[0].y);
  for (let i = 0; i <= 5; i++) {
    c.lineTo(outsidep[i % 5].x, outsidep[i % 5].y);
    c.lineTo(insidep[i % 5].x, insidep[i % 5].y);
  }
  c.stroke();
}
function Draw_Delta_In_Rect(c, w, h) {
  drawdelta(c, w, h);
}
function Draw_Rect_In_Rect(c, w, h) {
  c.fillRect(0, 0, w, h);
}
function Draw_Arc_In_Rect(c, w, h) {
  drawellipsetwo(c, w / 2, h / 2, w / 2, h / 2);
  //ellipse(0, h/2.0,w/2.0,h/2.0,0,0,2*Math.PI);
  c.fill();
}
function Draw_Star_In_Rect(c, w, h) {
  let insidep = new Array(),
    outsidep = new Array();
  let raw_width = 1902.113032,
    raw_height = 1809.016994,
    raw_centerx = 951.056516,
    raw_centery = 1000;
  let centerx = (raw_centerx * w) / raw_width,
    centery = (raw_centery * h) / raw_height;
  for (let i = 0; i < 5; i++) {
    insidep[i] = {
      x: Math.cos(((54 + i * 72) / 180) * Math.PI) * 500,
      y: -Math.sin(((54 + i * 72) / 180) * Math.PI) * 500,
    };

    outsidep[i] = {
      x: Math.cos(((18 + i * 72) / 180) * Math.PI) * 1000,
      y: -Math.sin(((18 + i * 72) / 180) * Math.PI) * 1000,
    };
    insidep[i].x *= w / raw_width;
    insidep[i].y *= h / raw_height;
    outsidep[i].x *= w / raw_width;
    outsidep[i].y *= h / raw_height;
    insidep[i].x += centerx;
    insidep[i].y += centery;
    outsidep[i].x += centerx;
    outsidep[i].y += centery;
  }
  c.beginPath();
  c.moveTo(outsidep[0].x, outsidep[0].y);
  for (let i = 0; i <= 5; i++) {
    c.lineTo(outsidep[i % 5].x, outsidep[i % 5].y);
    c.lineTo(insidep[i % 5].x, insidep[i % 5].y);
  }
  c.fill();
}
function FontSizeAddV(value) {
  //console.log(LastEditId);
  if (LastEditId != null) {
    let rawsize = ElementArray[LastEditId].TextDiv.style.fontSize.slice(0, -2);
    ElementArray[LastEditId].TextDiv.style.fontSize =
      parseInt(rawsize) + value + "px";
  }
}
function AlignChange() {
  if (LastEditId != null) {
    let nowalign = ElementArray[LastEditId].TextDiv.style.textAlign;
    //console.log(nowalign);
    if (nowalign == "left")
      ElementArray[LastEditId].TextDiv.style.textAlign = "center";
    else if (nowalign == "center")
      ElementArray[LastEditId].TextDiv.style.textAlign = "right";
    else if (nowalign == "right")
      ElementArray[LastEditId].TextDiv.style.textAlign = "left";
  }
}
function MoveToTop() {
  if (LastEditId != null) {
    BaseDiv.removeChild(ElementArray[LastEditId].FatherDiv);
    BaseDiv.appendChild(ElementArray[LastEditId].FatherDiv);
    let tempobj = ElementArray[LastEditId];
    ElementArray.splice(LastEditId, 1);
    ElementArray[ElementArray.length] = tempobj;
    LastEditId = null;
    InterAct = {
      ChosenDivId: null,
      MouseOffsetX: null,
      MouseOffsetY: null,
      ActMode: null,
    };
  }
}
function RemoveElement() {
  if (LastEditId != null) {
    BaseDiv.removeChild(ElementArray[LastEditId].FatherDiv);
    ElementArray.splice(LastEditId, 1);
    LastEditId = null;
    InterAct = {
      ChosenDivId: null,
      MouseOffsetX: null,
      MouseOffsetY: null,
      ActMode: null,
    };
  }
}
function NewElement(type, extradata) {
  let nid = ElementArray.length;
  ElementArray[nid] = {
    FatherDiv: null,
    Canvas: null,
    CanvasDiv: null,
    ShapeDiv: null,
    TextDiv: null,
    MainColor: null,
    Width: null,
    Height: null,
    ShapeDivSize: null,
    ElementType: null,
  };
  let tunit = ElementArray[nid];
  //创建元素
  tunit.FatherDiv = document.createElement("div");
  tunit.Canvas = document.createElement("canvas");
  tunit.CanvasDiv = document.createElement("div");
  tunit.ShapeDiv = document.createElement("div");
  tunit.TextDiv = document.createElement("div");
  tunit.MainColor = extradata ? extradata.Color : ColorRand();
  tunit.Width = extradata
    ? extradata.Width * parseInt(BaseDiv.style.width)
    : 200;
  tunit.Height = extradata
    ? extradata.Height * parseInt(BaseDiv.style.height)
    : 200;
  tunit.ShapeDivSize = 30;
  type = extradata ? extradata.Type : type;
  tunit.ElementType = type;
  //对fatherdiv进行属性设定
  //tunit.FatherDiv.style.border='2px '+tunit.MainColor+' solid';
  tunit.FatherDiv.style.left = extradata
    ? extradata.PosX * parseInt(BaseDiv.style.width) + "px"
    : Math.floor(
        Math.random() *
          (parseInt(BaseDiv.style.width) - ElementArray[nid].Width)
      ) + "px";
  tunit.FatherDiv.style.top = extradata
    ? extradata.PosY * parseInt(BaseDiv.style.height) + "px"
    : Math.floor(
        Math.random() *
          (parseInt(BaseDiv.style.height) - ElementArray[nid].Height)
      ) + "px";
  tunit.FatherDiv.style.width = tunit.Width + "px";
  tunit.FatherDiv.style.height = tunit.Height + "px";
  tunit.FatherDiv.style.position = "absolute";

  //对Canvas进行属性设定
  tunit.Canvas.width = tunit.Width - tunit.ShapeDivSize;
  tunit.Canvas.height = tunit.Height - tunit.ShapeDivSize;
  tunit.Canvas.style.left = "0px";
  tunit.Canvas.style.top = "0px";
  tunit.Canvas.style.position = "absolute";

  let ctx = tunit.Canvas.getContext("2d");
  ctx.fillStyle = ctx.strokeStyle = tunit.MainColor;
  if (type == 0) {
    Draw_Rect_In_Rect(ctx, tunit.Canvas.width, tunit.Canvas.height);
  } else if (type == 1) {
    Draw_Delta_In_Rect(ctx, tunit.Canvas.width, tunit.Canvas.height);
  } else if (type == 2) {
    Draw_Star_In_Rect(ctx, tunit.Canvas.width, tunit.Canvas.height);
  } else if (type == 3) {
    Draw_Arc_In_Rect(ctx, tunit.Canvas.width, tunit.Canvas.height);
  }
  //对CanvasDiv进行属性设定
  //tunit.CanvasDiv.style.border='2px '+tunit.MainColor+' solid';
  tunit.CanvasDiv.style.outline = "none";
  tunit.CanvasDiv.style.left = "0px";
  tunit.CanvasDiv.style.top = "0px";
  tunit.CanvasDiv.style.width = tunit.Width - tunit.ShapeDivSize + "px";
  tunit.CanvasDiv.style.height = tunit.Height - tunit.ShapeDivSize + "px";
  tunit.CanvasDiv.style.position = "absolute";

  //对shapediv进行属性设定
  tunit.ShapeDiv.style.border = "2px #000000 solid";
  tunit.ShapeDiv.style.backgroundColor = "#ffffff";
  tunit.ShapeDiv.style.left = tunit.Width - tunit.ShapeDivSize + "px";
  tunit.ShapeDiv.style.top = tunit.Height - tunit.ShapeDivSize + "px";
  tunit.ShapeDiv.style.width = tunit.ShapeDivSize + "px";
  tunit.ShapeDiv.style.height = tunit.ShapeDivSize + "px";
  tunit.ShapeDiv.style.position = "absolute";
  tunit.ShapeDiv.style.display = "none";

  //对textdiv进行属性设定
  tunit.TextDiv.style.left = "0px";
  tunit.TextDiv.style.top = "0px";
  tunit.TextDiv.style.width = tunit.Width - tunit.ShapeDivSize + "px";
  tunit.TextDiv.style.height = tunit.Height - tunit.ShapeDivSize + "px";
  tunit.TextDiv.style.position = "absolute";
  tunit.TextDiv.style.outline = "none";
  tunit.TextDiv.style.fontSize = extradata ? extradata.FontSize + "px" : "20px";
  tunit.TextDiv.style.textAlign = extradata ? extradata.TextAlign : "center";
  tunit.TextDiv.style.color = ReverseColor(tunit.MainColor);

  //为TextDiv添加事件
  /*
                    当在他上面按下鼠标时，记录当前的坐标差（鼠标坐标-左上角坐标），并使得该元件变为选中状态。
                    当移动鼠标时，chosenid的元件跟随移动，这是全局的事件！！
                    当抬起鼠标时，重置InterAct
                */
  tunit.TextDiv.addEventListener("mousedown", function (e) {
    for (let i = 0; i < ElementArray.length; i++) {
      if (Object.is(ElementArray[i], tunit)) {
        InterAct.ChosenDivId = i;
        InterAct.ActMode = 1;
        LastEditId = i;
      }
    }
    InterAct.MouseOffsetX =
      e.clientX - parseInt(tunit.FatherDiv.style.left.slice(0, -2));
    InterAct.MouseOffsetY =
      e.clientY - parseInt(tunit.FatherDiv.style.top.slice(0, -2));
  });
  //为shapediv添加事件
  /*
                    当在他上面按下鼠标时，记录当前的坐标差，并使得该元件变为选中状态
                    当移动鼠标时，chosenid的元件中，该div跟随移动，其他div跟随形状改变
                    当抬起鼠标时，重置InterAct,让canvas重画
               */
  tunit.ShapeDiv.addEventListener("mousedown", function (e) {
    for (let i = 0; i < ElementArray.length; i++) {
      if (Object.is(ElementArray[i], tunit)) {
        InterAct.ChosenDivId = i;
        InterAct.ActMode = 2;
        LastEditId = i;
      }
    }
    InterAct.MouseOffsetX =
      e.clientX - parseInt(tunit.ShapeDiv.style.left.slice(0, -2));
    InterAct.MouseOffsetY =
      e.clientY - parseInt(tunit.ShapeDiv.style.top.slice(0, -2));
  });

  //添加父子嵌套关系
  tunit.FatherDiv.appendChild(tunit.CanvasDiv);
  tunit.FatherDiv.appendChild(tunit.ShapeDiv);
  tunit.FatherDiv.appendChild(tunit.TextDiv);
  tunit.CanvasDiv.appendChild(tunit.Canvas);
  BaseDiv.appendChild(tunit.FatherDiv);
}
function SaveToServer() {
  //这个函数将项目保存至服务器
  let res={
    pic_num:P_Id,
    height:parseInt(BaseDiv.style.height),
    width:parseInt(BaseDiv.style.width),
    pic_name:P_Name,
    pic_detail:P_Description,
    pic_cover:BaseCanvas.toDataURL("image/png"),
    pic_data:new Array(),
  }
  let BaseWidth = parseFloat(BaseDiv.style.width),
    BaseHeight = parseFloat(BaseDiv.style.height);
  for (let i = 0; i < ElementArray.length; i++) {
    let tobj = {
      PosX: parseInt(ElementArray[i].FatherDiv.style.left) / BaseWidth,
      PosY: parseInt(ElementArray[i].FatherDiv.style.top) / BaseHeight,
      Id: i,
      Height: parseInt(ElementArray[i].FatherDiv.style.height) / BaseHeight,
      Width: parseInt(ElementArray[i].FatherDiv.style.width) / BaseWidth,
      Color: ElementArray[i].MainColor,
      Text: ElementArray[i].TextDiv.innerText,
      FontSize: ElementArray[i].TextDiv.style.fontSize.slice(0, -2),
      Type: ElementArray[i].ElementType,
      TextAlign: ElementArray[i].TextDiv.style.textAlign,
    };
    res.pic_data[i] = tobj;
  }
  let jsonres=JSON.stringify(res);
  xhr=new XMLHttpRequest();
  let url=SERVER_URL+'/api/project/change';
  xhr.onreadystatechange=Save_To_Server_Done;
  xhr.open('post',url,true);
  xhr.send(jsonres);

}
function Save_To_Server_Done(){
  if(xhr.readyState==4){
    if(xhr.status==200){
      let res=JSON.parse(xhr.responseText);
      console.log(res);
      alert(res.msg);
    }
  }
}
function ReadFromServer(){
  let res={
    pic_num:P_Id,
  }
  let jsonres=JSON.stringify(res);
  xhr=new XMLHttpRequest();
  let url=SERVER_URL+'/api/project/get';
  xhr.onreadystatechange=Read_From_Server_Done;
  xhr.open('post',url,true);
  xhr.send(jsonres);
}
function Read_From_Server_Done(){
    if(xhr.readyState==4){
    if(xhr.status==200){
      let res=JSON.parse(xhr.responseText);
      console.log(res);
      //alert(res.msg);
      P_Id=res.data.projects.id;
      U_Id=res.data.projects.owner;
      P_Name=res.data.projects.name;
      C_Time=res.data.projects.createTime;
      P_Description=res.data.projects.intro;
      BaseImg.src=res.data.projects.cover;
      //w,h
      let tcontent=JSON.parse(res.data.projects.content);
      for(let i=0;i<tcontent.length;i++){
        NewElement(0,tcontent[i]);
      }
      alert(res.msg);
    }
  }
}
window.onload = function () {
  BaseCanvasInput = document.getElementById("imginput");
  BaseCanvasInput.onchange = function () {
    var fileData = this.files[0];
    var reader = new FileReader();
    reader.readAsDataURL(fileData);
    reader.onload = function (e) {
      BaseImg.src = this.result;
      BaseImg.onload = function (e) {
        BaseCanvas.getContext("2d").drawImage(
          BaseImg,
          0,
          0,
          BaseCanvas.width,
          BaseCanvas.height
        );
        var url = BaseCanvas.toDataURL("image/png");
        //console.log(url);
      };
    };
  };
  var settings = document.getElementById("xa");
  console.log(settings);
  TopCanvasDiv.appendChild(settings);
  sortDiv1.style.backgroundColor="#2f75b5";
  sortDiv1.style.color="#FFFFFF";
  sortnum=1;
};
//54,96
Init();




//topstart
var sortnum=1;
var TopCanvasDiv = document.createElement("div");
TopCanvasDiv.style.left = (100 * 0) / 96 + "%";
TopCanvasDiv.style.top = (100 * 0) / 48 + "%";
TopCanvasDiv.style.width = (100 * 96) / 96 + "%";
TopCanvasDiv.style.height = (100 * 4) / 48 + "%";
TopCanvasDiv.style.position = "absolute";
document.body.appendChild(TopCanvasDiv);
</script>
