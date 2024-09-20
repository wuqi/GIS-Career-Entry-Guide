---
title: Osg-osg、osgb、osgt 文件
date: 2024-09-18T14:56:58Z
lastmod: 2024-09-18T14:56:58Z
---

> 原文地址 [https://www.cnblogs.com/FKdelphi/p/16835903.html](https://www.cnblogs.com/FKdelphi/p/16835903.html)

osg、其中 osgb 是 osg 的二进制格式；osgt 格式是 如下官网解释：

OSGT file is an OpenSceneGraph ASCII Scene Data. The OpenSceneGraph is an open source high performance 3D graphics toolkit, used by application developers in fields such as visual simulation, games, virtual reality, scientific visualization and modelling.
翻译：OSGT 文件是 OpenSceneGraph ASCII 场景数据。OpenSceneGraph 是一个开源的高性能 3D 图形工具包，供应用程序开发人员在视觉模拟、游戏、虚拟现实、科学可视化和建模等领域使用。

```
Group {
UniqueID Group_0      //Gourp名称
DataVariance STATIC   //不知道用来干嘛，一般都是static
cullingActive TRUE    //剔除阴影视点看不见的地方部分优化等等
num_children 1        //子节点数
Geode {               ////子节点是Geode节点（Geode节点是叶节点，它不会再有子节点，可以与任意多个Drawable的对象关联） 
 DataVariance DYNAMIC       
 name "cow.osg"        //Geode名称，应该是模型路径吧 就是文件名  
 cullingActive TRUE    //参与剔除  
 num_drawables 1       //可绘制元素Drawable对象的数目，1 
 Geometry {            //Gemetry，是可绘制对象类型之一。用指定顶点数据，绘制几何体。    
    DataVariance DYNAMIC   
        StateSet {          //渲染状态机。分为渲染属性和渲染模式两部分。是个状态值，直到子节点重新设置前，它的值一直沿节点树向下有效。     
            DataVariance STATIC      
            rendering_hint OPAQUE_BIN   //  渲染有关系 不透明      
            renderBinMode INHERIT       //渲染有关系 继承渲染状态      
            GL_CULL_FACE OFF            //和裁剪有关的   裁剪面     
            GL_LIGHTING ON              //灯光打开     
            Material {                  //材质相关        
                DataVariance STATIC       
                ColorMode OFF              //指定了质材的颜色跟踪属性 底下的四种      
                ambientColor 0.5 0.5 0.5 1    //环境光        
                diffuseColor 1 1 1 1          //漫反射      
                specularColor 1 1 1 1         //镜面反射       
                emissionColor 0 0 0 1         //自发光       
                shininess 1                   //光泽 0到1     
            }      
            textureUnit 0 {             //纹理贴图部分       
                GL_TEXTURE_GEN_S ON       
                GL_TEXTURE_GEN_T ON       
                GL_TEXTURE_2D ON        //打开2D的贴图       
                Texture2D {            //2D的贴图          
                    DataVariance STATIC         
                    file "Images/reflect.rgb" //贴图文件         
                    wrap_s REPEAT         
                    wrap_t REPEAT        
                    wrap_r REPEAT          
                    min_filter NEAREST_MIPMAP_LINEAR  //filter 线性MIPMAP        
                    mag_filter LINEAR       
                    internalFormatMode USE_IMAGE_DATA_FORMAT //格式模式        
                    subloadMode OFF  //分载模式关闭     
                    }        
                TexGen {      //纹理坐标生成
                    Texture coordinate generation       
                    DataVariance STATIC        
                    mode SPHERE_MAP //像镜子一样反射环境（此时这张纹理贴图相当于环境贴图）这里包括ObjectLinear, EyeLinear, SphereMap, CubeReflect, CubeNormal 模式等等       
                    }     
            }    
        }    
        useDisplayList TRUE     //显示模型的列表   
        Primitives 1 //图元    
        {      
            DrawArrayLengths TRIANGLE_STRIP 0 984  //三角图元构成画几何面     
            {        
                3        
                3       
                3       
                3     
            }    
        }   
        VertexArray 7772      //顶点坐标   
        {      
            4.76334 -1.36791 3.02949     
            4.85496 -1.36297 3.09889      
            4.73514 -1.38955 3.08412     
            4.92619 0.143477 2.29847   
        }   
        NormalBinding PER_VERTEX     //法向量绑定方式，每个顶点  
        NormalArray 7772 
        {     
            0.254622 -0.918791 -0.301648    
            0.440603 -0.85537 -0.272417    
            0.244499 -0.920072 -0.306084     
            0.506864 0.840135 -0.193035   
        }   
        ColorBinding OVERALL         //设置颜色的绑定方式   
        ColorArray Vec4Array 1      //颜色的值   
        {        0.8 0.8 0.8 1      }   
        TexCoordArray 0 Vec2Array 7772  //纹理UV坐标      {
            0 0      
            0 0     
            0 0     
            0 0  
        }
    }
  }
}

```

搜索

复制
