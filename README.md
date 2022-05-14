
<!DOCTYPE html>
<html lang="jp">
  <head>
    <meta charset="utf-8">
    <script>
      let canvas;
      let Magnification = 50;
      let FirstStrike=true;
      let MarubatsuTable=[["","",""],["","",""],["","",""]];
      function onLoad(){
        canvas = document.getElementById('sampleCanvas');
        if ( ! canvas || ! canvas.getContext ) {
          return false;
        }
        canvas.style.width = Magnification * 3+"px";
        canvas.style.hight = Magnification * 3+"px";

        // ＃を描画
        var context = canvas.getContext('2d');
        context.beginPath();
        context.moveTo(Magnification*0, Magnification*1);
        context.lineTo(Magnification*3, Magnification*1);
        context.moveTo(Magnification*0, Magnification*2);
        context.lineTo(Magnification*3, Magnification*2);
        context.moveTo(Magnification*1, Magnification*0);
        context.lineTo(Magnification*1, Magnification*3);
        context.moveTo(Magnification*2, Magnification*0);
        context.lineTo(Magnification*2, Magnification*3);
        context.stroke();

        // クリック処理
        canvas.addEventListener('click', onClick = (e) => {
          // クリックされたセルを検出
          let rect = e.target.getBoundingClientRect();
          x = Math.floor( (e.clientX - rect.left) / Magnification );
          y = Math.floor( (e.clientY - rect.top) / Magnification );
          if( MarubatsuTable[y][x] == "" )
          {
            if( FirstStrike )
            {
              MarubatsuTable[y][x] = "○";
              context.beginPath();
              context.arc( (x+0.5)*Magnification, (y+0.5)*Magnification, Magnification/3 , 0, 2 * Math.PI, false ) ;
              context.stroke();
            }
            else
            {
              MarubatsuTable[y][x] = "×";
              context.beginPath();
              context.moveTo((x+0.2)*Magnification, (y+0.2)*Magnification);
              context.lineTo((x+0.8)*Magnification, (y+0.8)*Magnification);
              context.moveTo((x+0.8)*Magnification, (y+0.2)*Magnification);
              context.lineTo((x+0.2)*Magnification, (y+0.8)*Magnification);
              context.stroke();
            }
            // 結果判定
            const decisionTable =
            [ [{x:0,y:0},{x:0,y:1},{x:0,y:2}],
              [{x:1,y:0},{x:1,y:1},{x:1,y:2}],
              [{x:2,y:0},{x:2,y:1},{x:2,y:2}],
              [{x:0,y:0},{x:1,y:0},{x:2,y:0}],
              [{x:0,y:1},{x:1,y:1},{x:2,y:1}],
              [{x:0,y:2},{x:1,y:2},{x:2,y:2}],
              [{x:0,y:0},{x:1,y:1},{x:2,y:2}],
              [{x:2,y:0},{x:1,y:1},{x:0,y:2}] ];
            decisionTable.forEach((a)=>
            {
              if( MarubatsuTable[a[0].y][a[0].x] + MarubatsuTable[a[1].y][a[1].x] + MarubatsuTable[a[2].y][a[2].x] == "○○○"
               || MarubatsuTable[a[0].y][a[0].x] + MarubatsuTable[a[1].y][a[1].x] + MarubatsuTable[a[2].y][a[2].x] == "×××" )
              {
                  context.beginPath();
                  context.moveTo( (a[0].x+0.5)*Magnification, (a[0].y+0.5)*Magnification);
                  context.lineTo( (a[2].x+0.5)*Magnification, (a[2].y+0.5)*Magnification);
                  context.stroke();
                  // 勝負がついたのでイベント削除
                  canvas.removeEventListener('click', onClick);
              } 
            });
            // 先攻後攻変更
            FirstStrike = !FirstStrike;
          }
          console.table(MarubatsuTable);
        }, false);
      }
      function Decision(){
      }
    </script>
  </head>
  <body onload="onLoad();">
    <canvas id="sampleCanvas" width="150" height="150"></canvas>
  </body>
</html>
