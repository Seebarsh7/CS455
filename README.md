# CS455 押题之神本人
# leetcode with C++, Python, Java
## Array  
1. flipping an image (flip 0 and 1)   
Exclusive Or: ^=
```C++
    vector<vector<int>> flipAndInvertImage(vector<vector<int>>& A) {
        for (auto & row : A){
            reverse(row.begin(), row.end());
        }// reverse()
        for (auto & row : A){
            for (int & i: row){
                i ^= 1;
            }
        }//exclusive or
        return A;
    }//flip and invert
```
```Java haimeixuene
class Solution {
    public int[][] flipAndInvertImage(int[][] A) {
        int C = A[0].length;
        for (int[] row: A)
            for (int i = 0; i < (C + 1) / 2; ++i) {
                int tmp = row[i] ^ 1;
                row[i] = row[C - 1 - i] ^ 1;
                row[C - 1 - i] = tmp;
            }

        return A;
    }
}
```

# 看这里啊朋友们
## 原子图
**用JMol读取.mol或者.xyz，本质上就是写一个txt**
```Java
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.PrintStream;

public class CircleA {

    public static void main(String[] args) throws IOException{

        float x,y,z = 0;
        float radian;
        float radius = 10;
        int count = 0;

        File f = new File("./" + "002" + ".txt"); //./的意思是默认根目录
        FileOutputStream fout = new FileOutputStream(f);
        PrintStream out = new PrintStream(fout);
        out.println("\n" + "Caffine");

        for(int theta = 0;theta < 360;theta+=5) {
            for (int phi = 0; phi < 360; phi += 5) {
                radian = (float) (theta * Math.PI / 180.0);
                float phiRadian = (float)(phi * Math.PI /180.0);
                x = (float) (radius* Math.sin(phiRadian) * Math.cos(radian)); //球面坐标系
                y = (float) (radius* Math.sin(phiRadian) * Math.sin(radian));
                z = (float)(radius*Math.cos(phiRadian));
                out.println("C   " + x + "   " + y + "   " + z);
                count += 1;
                System.out.println(count);
            }
        }
        out.close();
    }
}
```
## Folium，写一个3D .stl文件展现Folium 3D曲线   
### 在线展示stl：https://www.viewstl.com/   
```Java
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.PrintStream;

public class Folium {

    public Folium() {
    }

    private class Pt {
        double x, y, z;
    }// Pt

    public Folium.Pt calcPt(double u, double v) {

        Folium.Pt p = new Folium.Pt(); // we will fill and return this

        float uR = (float) (u * Math.PI / 180.0);
        float vR = (float) (v * Math.PI / 180);

        p.x = (float) (Math.cos(uR) * (2 * vR / Math.PI - Math.tanh(vR)));
        p.y = (float) (Math.cos(uR + 2 * Math.PI / 3) / Math.cosh(vR));
        p.z = (float) (Math.cos(uR - 2 * Math.PI / 3) / Math.cosh(vR));

        return p;
    }


    public void createSTL() throws IOException {

        File f = new File("./" + "F" + ".stl");
        FileOutputStream fout = new FileOutputStream(f);
        PrintStream out = new PrintStream(fout);


        out.println("solid"); // header

        for (int u = -180; u < 180; u += 5) {
            for (int v = -180; v < 180; v += 5) {
                // form an 'upper' tri
                out.println("facet normal 0 0 0");
                out.println("outer loop");

                // verts
                Pt v0 = calcPt(u, v);
                Pt v1 = calcPt(u, v + 5);
                Pt v2 = calcPt(u + 5, v);

                out.println("vertex " + v0.x + " " + v0.y + " " + v0.z);
                out.println("vertex " + v1.x + " " + v1.y + " " + v1.z);
                out.println("vertex " + v2.x + " " + v2.y + " " + v2.z);

                out.println("endloop");
                out.println("endfacet");

                // form a 'lower' tri
                out.println("facet normal 0 0 0");
                out.println("outer loop");

                // verts
                v0 = calcPt(u + 5, v);
                v1 = calcPt(u, v + 5);
                v2 = calcPt(u + 5, v + 5);

                out.println("vertex " + v0.x + " " + v0.y + " " + v0.z);
                out.println("vertex " + v1.x + " " + v1.y + " " + v1.z);
                out.println("vertex " + v2.x + " " + v2.y + " " + v2.z);

                out.println("endloop");
                out.println("endfacet");
            }// next x
        }// next y

        out.println("endsolid"); // footer

        // done!
        out.close();

    }// createSTL()

    public static void main(String[] args) {

        Folium f = new Folium();
        try {
            f.createSTL();
        } catch (Exception e) {}

    }
}
```
## TriGrid代码改动，改成两边高中间低的曲面
```Java
import java.io.File;
import java.io.FileOutputStream;
import java.io.PrintStream;
import java.io.IOException;

class TriGrid {

    int xRes=10, yRes=10;

/** 改了这里，计算distance */
    public float Dist(int i,int j){
        float z0;

        z0 = (float)Math.sin(Math.sqrt(i*i+j*j));
        return z0;
    }

    public void createFacets(PrintStream out) {

        for(int j=0;j<(yRes-1);j++) {
            for(int i=0;i<(xRes-1);i++) {

                // form an 'upper' tri, between (i,j),(i,j+1),(i+1,j)
                out.println("facet normal 0 0 0");
                out.println("outer loop");

                // verts
                out.println("vertex " + i + " " + j + " " + Dist(i,j));
                out.println("vertex " + i + " " + (j+1) + " " + Dist(i,j+1));
                out.println("vertex " + (i+1) + " " + j + " " + Dist(i+1,j));

                out.println("endloop");
                out.println("endfacet");

                // form a 'lower' tri, between (i+1,j), (i,j+1), (i+1,j+1)).
                out.println("facet normal 0 0 0");
                out.println("outer loop");

                // verts
                out.println("vertex " + (i+1) + " " + j + " " + Dist(i+1,j));
                out.println("vertex " + i + " " + (j+1) + " " + Dist(i,j+1));
                out.println("vertex " + (i+1) + " " + (j+1) + " " + Dist(i+1,j+1));

                out.println("endloop");
                out.println("endfacet");


            }// next x
        }// next y

    }// createFacets()




    public static void main(String[] args) throws IOException {


        File f = new File("./grid.stl");
        FileOutputStream fout = new FileOutputStream(f);
        PrintStream out = new PrintStream(fout);

        //now we can println() into out.
        out.println("solid"); // header

        // the actual facets are written out using a TriGrid object
        TriGrid tg = new TriGrid();
        tg.createFacets(out);

        out.println("endsolid"); // footer

        // done!
        out.close();
    }// main()

}// TriGrid
```
## wrl文件：3D的
**基本和stl之类的文件输出一致，也是改动x,y,z的坐标**
```Java
import java.io.File;
import java.io.FileOutputStream;
import java.io.PrintStream;
import java.io.IOException;
import java.util.Random;



class spheres {
  public static void main(String[] args) throws java.io.FileNotFoundException {
    
    int n=100; // try 1000, 10000 as well :)
    Random rng = new Random();
    
    // open file
    File f = new File("C:\\temp\\test.wrl");
    FileOutputStream fout = new FileOutputStream(f);
    PrintStream out = new PrintStream(fout);
```
**下面进行文件输出的部分，首先写header： ("#VRML 2.0 utf8")**
**底下用到了一个outputSph的函数，是它后面自己定义了一个，就是格式化输出**
```Java

    out.println("#VRML 2.0 utf8");
    
    // write geometry
    for(int i=0;i<n;i++) {
      double x = rng.nextFloat();// 0..1
      double y = rng.nextFloat();
      double z = rng.nextFloat();
      //System.out.println(x);
      outputSph(x,y,z,out);
    }// next 
    
    // close file
    out.close();
    
  }// main()
  
  public static void outputSph(double x, double y, double z, PrintStream out) {
    out.println("# " + x + "," + y + "," + z);
    out.println("Transform {");
    out.println("  translation " + x + " " + y + " " + z);
    out.println("    children [");
    
    outputShape(0.1,out);
    
    out.println("    ]"); // children
    out.println("}"); // Transform
  }// outputSph()
  
  public static void outputShape(double rad, PrintStream out) {
    out.println("      Shape {");
    
    out.println("        appearance Appearance {");
    out.println("          material Material {}");
    out.println("        }");
    
    out.println("        geometry Sphere {");
    out.println("          radius "+rad);
    out.println("        }");
    out.println("      }#Shape");
  }// outputShape()
}// class spheres
```

## Swing写JFrame, JPanel, JLabel的固定语法，复制粘贴基本就能用
```Java
JFrame frame = new JFrame();//先新建一个JFrame
JPanel p = new JPanel(); //再新建一个JPanel
p.setBackground(Color.WHITE); //这是JPanel的背景色
p.setPreferredSize(new Dimension(xResolution,yResolution)); //这是JPanel画布的大小
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); //这里是设置frame关闭的方式，可以当作固定短语复制粘贴
frame.add(p);//把JPanel加到JFrame里

BufferedImage surface = new BufferedImage(xResolution,yResolution,BufferedImage.TYPE_INT_RGB); //新建一个surface
JLabel view = new JLabel(new ImageIcon(surface));//新建一个以surface为参数的JLabel
view.setBorder(BorderFactory.createEmptyBorder(0, 0, 0, 0)); //无关紧要的话
view.setPreferredSize(new Dimension(xResolution,yResolution));
p.add(view);//把Jlabel view加到JPanel里 

Graphics g = surface.createGraphics(); //好了可以画图了！
```
**如果是喊你setGraphics()的话，就可以用Graphics自带的drawLine，drawOval之类的函数了**
**http://bytes.usc.edu/cs455saty/lectures/JavaLang_I/code/CanvasPlot/CanvasPlot.java**
```Java
/** 注意这个，此处是setGraphics */
Graphics g = surface.setGraphics(); 
if (drawArc) {
            g.setColor(Color.BLUE);
            int xx = random.nextInt(60);
            int yy = random.nextInt(40);
            drawArc(x,y,xx,yy,g);
        } else {
            drawNode(x,y,g);
        }
```
## 西加加怎么输出文件呢？
**include \<fstream\>!!!!**  
**使用ofstream + 名字，比如pgmFile，然后输出的时候和Java输出文件是一样的，把所有cout改成pgmFile，也就是你在往pgmFile里写东西** 
``` C++
    
    void writeFile(){
    ofstream pgmFile("./" + filename +".pgm"); // output image file we're creating //起名为ofstream+名字，比如这里的名字是pgmFile

    // 头文件！！！！！注意这里cout替换成了名为pgmFile的ofstream，下面也是一样的
    pgmFile << "P2" << endl;
    pgmFile << xRes << " " << yRes << endl; 
    pgmFile << 255 << endl; 

    // pixel data
    for(int i=0;i<yRes;i++){
        for(int j=0;j<xRes;j++){
            pgmFile << getPixel(j,i) << " ";
        }// next column
        pgmFile << endl;
    }// next row

    // 记得把文件关上！！！！！！！
    pgmFile.close();
}//writeFile()
```
### Java类似语法比较
**就是新建了一个FileOutputStream fout和PrintStream out，然后把所有System.out.println改成out.println!!! **
```Java
File f = new File("./" + "F" + ".stl");
FileOutputStream fout = new FileOutputStream(f);
PrintStream out = new PrintStream(fout);


out.println("solid"); // header
```
### Java ppmFile 参考作业4，西加加 pgmFile参考作业2


## 西加加和Java的读文件
### 西加加： #include \<fstream\>, ifstream 这个文件流的名字+路径名，以及inFile >> token（程序自动把文件按照空格分了）   
```C++
#include<iostream>
#include<fstream>

using namespace std;

int main()
{
    ifstream inFile("C:/temp/feep.pgm");
    char a[80];
    
    //这里的意思是如果inFile不存在的话，就略略略
    if(!inFile)
    {
        cerr << "Error opening file.." << endl;
        return(1);
    }
    
    //这里的意思是如果inFile还没有到头的话，使用
    while(!inFile.eof())
    {
        inFile >> a; // 读取：从文件里读一个单词（token），然后把这个单词存到名为a的变量里
        cout << a << endl; // 再把a打印出来
    }

    inFile.close(); //记得close！！！！！！

    return(0);
}// main()
```

### Java文件读写: URL fetching
```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;

public class URLReader {
  public static void main(String[] args) throws Exception {
    URL myURL = new URL("http://www.usc.edu/");
    
    //注意这句话！！！！！新建了一个BufferedRead的文件
    BufferedReader in = new BufferedReader(new InputStreamReader(myURL.openStream()));

    String inputLine;

    while ((inputLine = in.readLine()) != null)
      System.out.println(inputLine);

    in.close();
  }
}// URLReader
```

### Java读txt: 也是用BufferReader
```Java
import java.io.*;
class readFiles {
  public static void main(String args[]) throws Exception {
    FileReader fr = new FileReader("WeThePeople.txt");
    BufferedReader br = new BufferedReader(fr);
    String s;
    while((s = br.readLine()) != null) {
      System.out.println(s);
    }
    fr.close();
  }
}// readFiles()
```
### Java里怎么count Alice出现的个数？
**用java.util.StringTokenizer(splitThis, delim); 或者String object自带的split(delim)。 delim的意思就是分隔符**  
```Java
public class tokenizing {
  
  public static void main(String[] args) {
    String delim = ","; // our "delimiter" - what to use, for splitting 
    String splitThis = "one,two,,three,four,,five"; // our string to split
    
    System.out.println("StringTokenizer:");
    java.util.StringTokenizer st = new java.util.StringTokenizer(splitThis, delim);
    while (st.hasMoreElements()) {
      System.out.println(" "+st.nextElement());
    }
    
    System.out.println("\nsplit(): ");
    String[] tokens = splitThis.split(delim);
    int tokenCount = tokens.length;
    for (int j=0; j<tokenCount; j++) {
      System.out.println(" "+tokens[j]);
    }
  }
}// tokenizing
```

## 杂物：算pi的值和算几何的面积
```Java

public class Rnd {

    private double last = 0.1; // latest # in our sequence
    private double seed = 0.1; // a seed value, between 0.0 and 1.0
    private double mpyer = 309.0, adder = 0.203125; // for our LCG formula
    private int count = 0; // how many nums served so far

    public Rnd(double s) {
        seed = s;
        last = s;
    }// Rnd

    public Rnd() {
    } //default

    public double random() {
        last = mpyer * last + adder;
        last -= (int) last; // same as last %= 1.0;
        count++;
        return last;
    }// random

    public int getCount() {
        return count;
    }// getCount()

    public void reset() {
        last = seed;
        count = 0;
    }// reset

    public static void main(String[] args) {

        int nThrows = 100000000;
        int nInside = 0;
        Rnd randomGenerator = new Rnd(0.257);//0.257可以换成别的数字！！！
        for (int i = 0; i < nThrows; i++) {
            double x = randomGenerator.random();
            double y = randomGenerator.random();
            if ((x * x + y * y) < 1) {
                nInside += 1;
            }
        }
        double pi = 4.0 * nInside / nThrows;
        System.out.println(pi);
    }//Rnd
}
```
## 蒙特卡洛求几何面积
**https://blog.csdn.net/RicheyLee/article/details/51066743**
```Java
class monte{
    private double length = 10;
    private int total = 10000;
    
    //也可以自己写一个constructor， this.length = xxx, this.total = xxx
    public monte(){} //default
    
    public static void main(String[] args) {
        Random rng = new Random();
        int length = 10;
        int nThrows = 100000;
        monte area = new monte(length, nThrows);
        int nInside = 0;
        double lengthSquare = length * length;
        for(int i = 0; i <nThrows; i++){
            double x = rng.nextDouble() * 10;
            double y = rng.nextDouble() * 10;
            //pseudo code
            if(y<=2-x.^2){
                nInside ++;
            }else if(y.^3>=x.^2){
                nInside ++;
            }
        }//for
        //然后这道题的本质是先求pi
        //再求面积
    
}//main
```
