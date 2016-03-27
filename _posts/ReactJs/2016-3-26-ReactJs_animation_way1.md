---
layout: post
title: ReactJs Animation-方式1-通过timer
categories: ReactJs
---

## 动画效果

<html>
    <head>
        <script src="//cdn.bootcss.com/react/0.14.7/react.js"></script>
        <script src="//cdn.bootcss.com/react/0.14.7/react-dom.js"></script>
        <script src="../build/browser.min.js"></script>
    </head>
    <body>
        <div id="ticker" style="font-size:16px;"></div>
        <script type="text/babel">
            var Timer = React.createClass({
                getInitialState: function() {
                    return {
                        secondsElapsed: 0,
                        R: 255,
                        G: 0,
                        B: 160,
                        A: 1,
                        Rmode: 'down',
                        Gmode: 'none',
                        Bmode: 'none',
                        X: 0,
                        Xmode: 'up',
                        Y: 0,
                        Ymode: 'up',
                        RGBA: 'rgba(0,0,0,0)',
                        width: "1%",
                        height: "1%",
                        width_num: 2,
                        height_num: 2,
                        Radius: 1,
                        RadiusMode: 'up',
                        WidthMode: 'up'
                    };
                },
                addR: function(R, Rmode) {
                    var nRmode = Rmode;
                    var nR = R + 1;
                    if( nR > 255 ){
                        nRmode = 'down';
                    }
                    return [ nR, nRmode ];
                },
                decR: function(R, Rmode, Gmode) {
                    var nRmode = Rmode;
                    var nGmode = Gmode;
                    var nR = R - 1;
                    if( nR < 0 ){
                        nRmode = 'up';
                        nGmode = 'up';
                    }
                    return [ nR, nRmode , nGmode];
                },
                tick: function() {
                    var R = this.state.R;
                    var G = this.state.G;
                    var B = this.state.B;
                    var A = this.state.A;
                    var Rmode = this.state.Rmode;
                    var Gmode = this.state.Gmode;
                    var Bmode = this.state.Bmode;
                    var Radius = this.state.Radius;
                    var RadiusMode = this.state.RadiusMode;
                    var WidthMode = this.state.WidthMode;
                    switch(Rmode){
                        case 'up':
                            var temp = this.addR(R, Rmode);
                            R = temp[0];
                            Rmode = temp[1];
                            break;
                        case 'down':
                            var temp = this.decR(R, Rmode, Gmode);
                            R = temp[0];
                            Rmode = temp[1];
                            Gmode = temp[2];
                            break;
                    }
                    switch(Gmode){
                        case 'up':
                            var temp = this.addR(R, Rmode);
                            R = temp[0];
                            Rmode = temp[1];
                            break;
                        case 'down':
                            var temp = this.decR(R, Rmode, Gmode);
                            R = temp[0];
                            Rmode = temp[1];
                            Gmode = temp[2];
                            break;
                    }
                    if( Gmode == 'up' ){
                        G = G + 1;
                        if( G == 255){
                            Gmode = 'down';
                        }
                    }else if( Gmode == 'down' ){
                        G = G - 1;
                        if( G == 0 ){
                            Gmode = 'up';
                            Bmode = 'down';
                        }
                    }
                    if( Bmode == 'up' ){
                        B = B + 1;
                        if( B == 255 ) {
                            Bmode = 'down';
                        }
                    }else if ( Bmode == 'down' ){
                        B = B - 1;
                        if( B == 0 ){
                            Bmode = 'up';
                        }
                    }
                    var X = this.state.X;
                    var Y = this.state.Y;
                    var Xmode = this.state.Xmode;
                    var Ymode = this.state.Ymode;
                    if( Xmode == 'up' ){
                        X = X + 3;
                        if( X > 1176 ){
                            Xmode = 'down';
                        }
                    }else if( Xmode == 'down' ){
                        X = X - 2;
                        if( X < 0 ){
                            Xmode = 'up';
                        }
                    }
                    if( Ymode == 'up' ){
                        Y = Y + 1;
                        if(Y > 308) {
                            Ymode = 'down';
                        }
                    } else if (Ymode == 'down'){
                        Y = Y - 1;
                        if(Y < 0){
                            Ymode = 'up';
                        }
                    }
                    var rgba_str = 'rgba(' + R + ',' + G + ',' + B + ','+ A + ')';
                    var width_temp_num = this.state.width_num;
                    var height_temp_num = this.state.height_num;
                    if( WidthMode== 'up' ) {
                        width_temp_num = this.state.width_num + 0.5;
                        height_temp_num = this.state.height_num + 0.5;
                        if( width_temp_num > 100 || height_temp_num > 100){
                            WidthMode = 'down';
                        }
                    }
                    if(WidthMode == 'down'){
                        width_temp_num = this.state.width_num - 0.5;
                        height_temp_num = this.state.height_num - 0.5;
                        if( width_temp_num < 0 || height_temp_num < 0){
                            WidthMode = 'up';
                        }
                    }
                    if( RadiusMode == 'up' ){
                        Radius = Radius + 1;
                        if(Radius == 150){
                            RadiusMode = 'down';
                        }
                    }else if(RadiusMode == 'down' ){
                        Radius = Radius - 1;
                        if(Radius == 0 ){
                            RadiusMode = 'up';
                        }
                    }
                    var width_str = width_temp_num + "%";
                    var height_str = height_temp_num + "%";
                    this.setState({
                        secondsElapsed: this.state.secondsElapsed + 1,
                        RGBA: rgba_str,
                        width: width_str,
                        height: height_str,
                        width_num: width_temp_num,
                        R: R,
                        G: G,
                        B: B,
                        A: A,
                        Rmode: Rmode,
                        Gmode: Gmode,
                        Bmode: Bmode,
                        WidthMode: WidthMode,
                        X: X,
                        Xmode: Xmode,
                        Y: Y,
                        Ymode: Ymode,
                        Radius: Radius,
                        RadiusMode: RadiusMode
                    });
                },
                  componentDidMount: function() {
                    this.interval = setInterval(this.tick, 1);
                  },
                  componentWillUnmount: function() {
                    clearInterval(this.interval);
                  },
                  render: function() {
                    return (
                      <div >
                        Seconds Elapsed: {this.state.secondsElapsed}
                        <br />
                        Color: {this.state.RGBA}
                        <br />
                        Width: {this.state.width}
                        <br />
                        Height: {this.state.height}
                        <br />
                        X: {this.state.X}
                        <br />
                        Y: {this.state.Y}
                        <br />
                        Rmode: {this.state.Rmode}
                        <br />
                        Gmode: {this.state.Gmode}
                        <br />
                        Bmode: {this.state.Bmode}
                        <br />
                        RadiusMode: {this.state.RadiusMode}
                        <br />
                        Radius: {this.state.Radius}
                      </div>
                    );
                  }
                });

            ReactDOM.render(
                <Timer />, 
                document.getElementById('ticker')
            );

        </script>
    </body>
</html>

```
var Timer = React.createClass({
                getInitialState: function() {
                    return {
                        secondsElapsed: 0,
                        R: 255,
                        G: 0,
                        B: 160,
                        A: 1,
                        Rmode: 'down',
                        Gmode: 'none',
                        Bmode: 'none',
                        X: 0,
                        Xmode: 'up',
                        Y: 0,
                        Ymode: 'up',
                        RGBA: 'rgba(0,0,0,0)',
                        width: "1%",
                        height: "1%",
                        width_num: 2,
                        height_num: 2,
                        Radius: 1,
                        RadiusMode: 'up',
                        WidthMode: 'up'
                    };
                },
                addR: function(R, Rmode) {
                    var nRmode = Rmode;
                    var nR = R + 1;
                    if( nR > 255 ){
                        nRmode = 'down';
                    }
                    return [ nR, nRmode ];
                },
                decR: function(R, Rmode, Gmode) {
                    var nRmode = Rmode;
                    var nGmode = Gmode;
                    var nR = R - 1;
                    if( nR < 0 ){
                        nRmode = 'up';
                        nGmode = 'up';
                    }
                    return [ nR, nRmode , nGmode];
                },
                tick: function() {
                    var R = this.state.R;
                    var G = this.state.G;
                    var B = this.state.B;
                    var A = this.state.A;
                    var Rmode = this.state.Rmode;
                    var Gmode = this.state.Gmode;
                    var Bmode = this.state.Bmode;
                    var Radius = this.state.Radius;
                    var RadiusMode = this.state.RadiusMode;
                    var WidthMode = this.state.WidthMode;
                    switch(Rmode){
                        case 'up':
                            var temp = this.addR(R, Rmode);
                            R = temp[0];
                            Rmode = temp[1];
                            break;
                        case 'down':
                            var temp = this.decR(R, Rmode, Gmode);
                            R = temp[0];
                            Rmode = temp[1];
                            Gmode = temp[2];
                            break;
                    }
                    switch(Gmode){
                        case 'up':
                            var temp = this.addR(R, Rmode);
                            R = temp[0];
                            Rmode = temp[1];
                            break;
                        case 'down':
                            var temp = this.decR(R, Rmode, Gmode);
                            R = temp[0];
                            Rmode = temp[1];
                            Gmode = temp[2];
                            break;
                    }
                    if( Gmode == 'up' ){
                        G = G + 1;
                        if( G == 255){
                            Gmode = 'down';
                        }
                    }else if( Gmode == 'down' ){
                        G = G - 1;
                        if( G == 0 ){
                            Gmode = 'up';
                            Bmode = 'down';
                        }
                    }
                    if( Bmode == 'up' ){
                        B = B + 1;
                        if( B == 255 ) {
                            Bmode = 'down';
                        }
                    }else if ( Bmode == 'down' ){
                        B = B - 1;
                        if( B == 0 ){
                            Bmode = 'up';
                        }
                    }
                    var X = this.state.X;
                    var Y = this.state.Y;
                    var Xmode = this.state.Xmode;
                    var Ymode = this.state.Ymode;
                    if( Xmode == 'up' ){
                        X = X + 3;
                        if( X > 1176 ){
                            Xmode = 'down';
                        }
                    }else if( Xmode == 'down' ){
                        X = X - 2;
                        if( X < 0 ){
                            Xmode = 'up';
                        }
                    }
                    if( Ymode == 'up' ){
                        Y = Y + 1;
                        if(Y > 308) {
                            Ymode = 'down';
                        }
                    } else if (Ymode == 'down'){
                        Y = Y - 1;
                        if(Y < 0){
                            Ymode = 'up';
                        }
                    }
                    var rgba_str = 'rgba(' + R + ',' + G + ',' + B + ','+ A + ')';
                    var width_temp_num = this.state.width_num;
                    var height_temp_num = this.state.height_num;
                    if( WidthMode== 'up' ) {
                        width_temp_num = this.state.width_num + 0.5;
                        height_temp_num = this.state.height_num + 0.5;
                        if( width_temp_num > 100 || height_temp_num > 100){
                            WidthMode = 'down';
                        }
                    }
                    if(WidthMode == 'down'){
                        width_temp_num = this.state.width_num - 0.5;
                        height_temp_num = this.state.height_num - 0.5;
                        if( width_temp_num < 0 || height_temp_num < 0){
                            WidthMode = 'up';
                        }
                    }
                    if( RadiusMode == 'up' ){
                        Radius = Radius + 1;
                        if(Radius == 150){
                            RadiusMode = 'down';
                        }
                    }else if(RadiusMode == 'down' ){
                        Radius = Radius - 1;
                        if(Radius == 0 ){
                            RadiusMode = 'up';
                        }
                    }
                    var width_str = width_temp_num + "%";
                    var height_str = height_temp_num + "%";
                    this.setState({
                        secondsElapsed: this.state.secondsElapsed + 1,
                        RGBA: rgba_str,
                        width: width_str,
                        height: height_str,
                        width_num: width_temp_num,
                        R: R,
                        G: G,
                        B: B,
                        A: A,
                        Rmode: Rmode,
                        Gmode: Gmode,
                        Bmode: Bmode,
                        WidthMode: WidthMode,
                        X: X,
                        Xmode: Xmode,
                        Y: Y,
                        Ymode: Ymode,
                        Radius: Radius,
                        RadiusMode: RadiusMode
                    });
                },
                  componentDidMount: function() {
                    this.interval = setInterval(this.tick, 1);
                  },
                  componentWillUnmount: function() {
                    clearInterval(this.interval);
                  },
                  render: function() {
                    return (
                      <div >
                        Seconds Elapsed: {this.state.secondsElapsed}
                        <br />
                        Color: {this.state.RGBA}
                        <br />
                        Width: {this.state.width}
                        <br />
                        Height: {this.state.height}
                        <br />
                        X: {this.state.X}
                        <br />
                        Y: {this.state.Y}
                        <br />
                        Rmode: {this.state.Rmode}
                        <br />
                        Gmode: {this.state.Gmode}
                        <br />
                        Bmode: {this.state.Bmode}
                        <br />
                        RadiusMode: {this.state.RadiusMode}
                        <br />
                        Radius: {this.state.Radius}
                        <div style={{position:'absolute',left:this.state.X,top:this.state.Y,borderRadius:this.state.Radius,fontSize:18,backgroundColor:this.state.RGBA,width:200,height:200,textAlign: 'center'}}>
                        </div>
                        <div style={{position:'absolute',right:this.state.X,bottom:this.state.Y,borderRadius:this.state.Radius,fontSize:18,backgroundColor:this.state.RGBA,width:200,height:200,textAlign: 'center'}}>
                        </div>
                      </div>
                    );
                  }
                });
```



by: 潘尚 <br />
time: 2016.3.26

