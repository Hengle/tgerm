---
title: Away3D 武器刀光的实现
layout: post
tags: [Away3D, 刀光]
---
<span style="color: #ff6600;"><strong>原创博文，转载请声明</strong></span>

感谢网友**@panda_星**提供的例子以及模型文件，很高兴认识关注该Blog的网友们。并非常感谢你们对文章的反馈。

效果图：

![image](../images/wp-content/uploads/2014/05/WeaponTaril.jpg)

[打开Demo][1]

实现刀光有两种方式：1.利用粒子来做，U3D里就有一个现有的组件来做这个效果而且貌似就是粒子特效。2.动态采集武器的运动轨迹来生成刀光用Geometry，然后贴上刀光贴图。上面的Demo是基于第二种的实现。

第一步：你需要有两个点作为采集点，例子里也就是刀尖和刀把对应的两根骨骼。在武器运动的同时收集这两根骨骼的位置。

第二步：这一步比较重要的一点就是需要对收集的点做平滑处理，也就是先将两帧之间的点均分，并调整点之间的距离为武器的长度。这样生成的Geometry就是平滑的了。

第三步：根据设定的最大Geometry数量来剔除前面生成的多余的Geometry,这样刀光也就有了跟随效果。

第四步：计算每个Geometry对应的UV坐标，然后上传，搞定。

看代码最清楚了。

WeaponTaril.as

<pre class="brush:as3">package copper3d.effect.weapon
{
    import flash.events.Event;
    import flash.geom.Vector3D;

    import away3d.animators.SkeletonAnimator;
    import away3d.animators.data.JointPose;
    import away3d.animators.data.Skeleton;
    import away3d.containers.View3D;
    import away3d.core.base.Geometry;
    import away3d.core.base.SubGeometry;
    import away3d.core.math.Vector3DUtils;
    import away3d.entities.Mesh;
    import away3d.events.AnimatorEvent;
    import away3d.materials.TextureMaterial;

    /**
     * 武器拖尾 
     * @author vancopper
     * 
     */ 
    public class WeaponTaril
    {
        private var _view3D:View3D;
        private var _tarilMat:TextureMaterial;
        private var _tarilSkeletonAni:SkeletonAnimator;
        private var _startBoneIndex:int;
        private var _endBoneIndex:int;
        private var _sJointPose:JointPose;
        private var _eJointPose:JointPose;
        private var _currentAniName:String;//当前播放的动作
        private var _tarilMesh:Mesh;
        private var _samplingMeshV:Vector.&lt;Mesh&gt; = new Vector.&lt;Mesh&gt;();// 采样
        private var _samplingGap:int = 20;//采样间隔为 50ms 未使用
        private var _timestamp:int;//时间戳
        private var _maxSamples:int = 60;
        private var _divide:int = 8;
        private var _weaponDis:Number = NaN;

        private var _isPlaying:Boolean;

        private var _bindingTags:Vector.&lt;String&gt;;

        public function WeaponTaril(view3d:View3D, mat:TextureMaterial)
        {
            _view3D = view3d;
            _tarilMat = mat;
        }

        /**
         * 显示武器拖尾
         * @param skeletonAin 要跟踪的SkeletonAnimator
         * @param startTag 用来生成拖尾的第一个骨骼点
         * @param endTag    用来生成拖尾的第二个骨骼点
         * 
         */     
        public function startTaril(mesh:Mesh, skeletonAni:SkeletonAnimator, startTag:String, endTag:String):void
        {
            if(!skeletonAni){throw new Error("SkeletonAnimator is Null");}

            _tarilSkeletonAni = skeletonAni;
            var skeleton:Skeleton = _tarilSkeletonAni.skeleton;
            if(!skeleton){throw new Error("skeleton is Null");}

            _weaponDis = NaN;
            _startBoneIndex = skeleton.jointIndexFromName(startTag);
            _endBoneIndex = skeleton.jointIndexFromName(endTag);
            if(_view3D)
            {
                _view3D.addEventListener(Event.EXIT_FRAME, onExitFrame);
            }
            _tarilMesh = mesh;
            _tarilSkeletonAni.addEventListener(AnimatorEvent.START, onAniStart);
        }

        /**
         * 停止拖尾 
         * 
         */     
        public function stopTaril():void
        {
            if(!_isPlaying)return;
            _isPlaying = false;
            if(_samplingMeshV)
            {
                for(var i:int = 0; i &lt; _samplingMeshV.length; i++)
                {
                    var mesh:Mesh = _samplingMeshV[i];
                    if(mesh && mesh.parent)
                    {
                        mesh.parent.removeChild(mesh);
                        mesh.dispose();
                    }
                }
            }
            if(_view3D)_view3D.removeEventListener(Event.EXIT_FRAME, onExitFrame);
//          _timestamp = getTimer();
            if(_tarilSkeletonAni)_tarilSkeletonAni.removeEventListener(AnimatorEvent.START, onAniStart);
            _tarilSkeletonAni = null;
        }

        public function dispose():void
        {
            //TODO:
        }

        /**
         * 要显示拖尾效果的动作 
         */
        public function get bindingTags():Vector.&lt;String&gt;
        {
            return _bindingTags;
        }

        /**
         * @private
         */
        public function set bindingTags(value:Vector.&lt;String&gt;):void
        {
            _bindingTags = value;
        }

        /**
         * 武器拖尾的采样间隔 
         */
        public function get samplingGap():int
        {
            return _samplingGap;
        }

        /**
         * @private
         */
        public function set samplingGap(value:int):void
        {
            _samplingGap = value;
        }

        /**
         * 最大采样值（可生成面片的最大数） 
         */
        public function get maxSamples():int
        {
            return _maxSamples;
        }

        /**
         * @private
         */
        public function set maxSamples(value:int):void
        {
            _maxSamples = value;
        }

        private function onExitFrame(e:Event):void
        {
            if(!bindingTags || bindingTags.length == 0)return;

            if(_tarilSkeletonAni.globalPose.jointPoses.length &gt; 0 && !_sJointPose && !_eJointPose)
            {
                _sJointPose = _tarilSkeletonAni.globalPose.jointPoses[_startBoneIndex];
                _eJointPose = _tarilSkeletonAni.globalPose.jointPoses[_endBoneIndex];
            }

            if(bindingTags.indexOf(_currentAniName) == -1)
            {
                stopTaril();
                return;
            }
            if(_sJointPose && _eJointPose)
            {
                samplingMesh();
            }
        }

        /**
         * 插值 
         * 
         */     
        private function interpolation():void
        {
            var s1:Vector3D = _tempV[0];
            var e1:Vector3D = _tempV[1];
            var s2:Vector3D = _tempV[2];
            var e2:Vector3D = _tempV[3];

            if(isNaN(_weaponDis))
            {
                _weaponDis = Vector3D.distance(s1, e1);
            }

            if(s1.nearEquals(s2, 10) || e1.nearEquals(e2, 10))
            {
                _posV = _tempV.concat();
                return;
            }

            var v1:Vector.&lt;Vector3D&gt; = Vector3DUtils.divide(s1, s2, _divide);
            var v2:Vector.&lt;Vector3D&gt; = Vector3DUtils.divide(e1, e2, _divide);
            for(var i:int = 0; i &lt; _divide + 1; i++)
            {
                _posV.push(v1[i]);
                _posV.push(getEndVector3D(v1[i], v2[i], _weaponDis));
            }
        }

        private function getEndVector3D(p1:Vector3D, p2:Vector3D, mDistance:Number ):Vector3D
        {
            var v:Vector3D = new Vector3D();
            var distance:Number = Vector3D.distance(p1, p2);
            var theta:Number = Math.asin((p2.z-p1.z)/distance);

            v.x = p1.x+ ((mDistance/Math.cos(theta))/distance)*(p2.x-p1.x);
            v.y = p1.y+ ((mDistance/Math.cos(theta))/distance)*(p2.y-p1.y);
            //TODO: z轴变化 会导致过度变形 
            v.z = p2.z;//p1.z+ ((mDistance/Math.cos(theta))/distance)*(p2.z-p1.z);
            return v;
        }

        private var _tempV:Vector.&lt;Vector3D&gt; = new Vector.&lt;Vector3D&gt;();
        private var _posV:Vector.&lt;Vector3D&gt; = new Vector.&lt;Vector3D&gt;();
        private function samplingMesh():void
        {
            _tempV.push(_sJointPose.toMatrix3D().position);
            _tempV.push(_eJointPose.toMatrix3D().position);
            if(_tempV.length &gt;= 4 && _tempV.length % 2 == 0)
            {
                interpolation();
                _tempV.shift();
                _tempV.shift();
            }

            if(_posV.length &gt; 0)
            {
                var num:int = _posV.length/2 - 1;
                var i:int = 0;
                var j:int = 0;
                for( i = 0; i &lt; num; i++)
                {
                    var vData:Vector.&lt;Number&gt; = new Vector.&lt;Number&gt;();
                    for(j = 0; j &lt; 4; j++)
                    {
                        vData.push(_posV[i*2 + j].x, _posV[i*2 +j].y, _posV[i*2 + j].z); 
                    }

                    var geometry:SubGeometry = new SubGeometry();
                    geometry.updateVertexData(vData);
                    geometry.updateIndexData(Vector.&lt;uint&gt;([0,1,3,0,2,3]));
                    var geo:Geometry = new Geometry();
                    geo.addSubGeometry(geometry);
                    var tempmesh:Mesh = new Mesh(geo, _tarilMat);
                    _tarilMesh.addChild(tempmesh);
                    _samplingMeshV.push(tempmesh);
                }
            }

            for (var n:int = 0; n &lt; _samplingMeshV.length; n++) 
            {
                var uvData:Vector.&lt;Number&gt; = Vector.&lt;Number&gt;([1-n/_maxSamples,0,
                    1-n/_maxSamples,1, 
                    1-(n+1)/_maxSamples,0, 
                    1-(n+1)/_maxSamples,1])
                SubGeometry(_samplingMeshV[n].geometry.subGeometries[0]).updateUVData(uvData)
            }

            if(_samplingMeshV.length &gt; _maxSamples)
            {
                var loop:int = _samplingMeshV.length - _maxSamples;
                while(loop --)
                {
                    var clearMesh:Mesh = _samplingMeshV.shift();
                    _tarilMesh.removeChild(clearMesh);
                    clearMesh.dispose();
                }
            }

            var len:int = _posV.length;
            if(len)
            {
                _tempV[0] = _posV[len - 2];
                _tempV[1] = _posV[len - 1];
                _posV.length = 0;
            }   
        }

        private function onAniStart(event:AnimatorEvent):void
        {
            _isPlaying = true;
            _currentAniName = _tarilSkeletonAni.activeAnimationName;
        }
    }
}</pre>

在Vector3DUtils.as里添加一个工具方法

<pre class="brush:as3">public static function divide(startVal:Vector3D, endVal:Vector3D, divideNum:int):Vector.&lt;Vector3D&gt;
{
    var result:Vector.&lt;Vector3D&gt; = new Vector.&lt;Vector3D&gt;();

    var stepx:Number = (endVal.x - startVal.x)/divideNum;
    var stepy:Number = (endVal.y - startVal.y)/divideNum;
    var stepz:Number = (endVal.z - startVal.z)/divideNum;

    var step:int = 1;
    var index:int = 1;
    var scalestep:Vector3D;
    result[0] = startVal;

    while(step &lt; divideNum)
    {
        scalestep = new Vector3D();
        scalestep.x = startVal.x + (stepx*step);
        scalestep.y = startVal.y + (stepy*step);
        scalestep.z = startVal.z + (stepz*step);
        result[step] = scalestep;
        step ++;
    }

    result[step] = endVal;

    return result;
}</pre>

用法：

<pre class="brush:as3">weaponT = new WeaponTaril(view, bodyMaterial);
weaponT.startTaril(mesh, animator, "WQ", "WQ1");//后面两个参数为武器上骨骼的名字
weaponT.bindingTags = Vector.&lt;String&gt;(["atk"]);//要播放刀光的动作名称
animator.play("atk");</pre>

 [1]: http://blog.as3er.com/demo/WeaponTarilTest.html