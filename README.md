# TankGame
This is game about tank.I think this is different with others.
/**
 * 坦克大战1.0
 * 功能：1.画出坦克
 */
package TankGame2;

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.*;
public class MyTankGame2 extends JFrame{

	/**
	 * @param args
	 */
	MyPanel p=null;
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MyTankGame2 Game=new MyTankGame2();
	}
	//构造函数
	public MyTankGame2()
	{
		p = new MyPanel();
		
		this.add(p);
		
		Thread t=new Thread(p);
		t.start();
		//注册监听
		this.addKeyListener(p);
		
		this.setSize(600, 400);
		this.setVisible(true);
	}
}

//面板（）
class MyPanel extends JPanel implements KeyListener,Runnable
{
	
	//定义一个坦克
	MyTank mytank=null;
	
	//定义敌人的坦克组
	Vector<EnemyTank> ets=new Vector<EnemyTank>();
	
	int enSize=3;
	
	//构造函数
	public MyPanel()
	{
		
		mytank=new MyTank(150,150);
		
		//初始化敌人坦克
		for(int i=0;i<enSize;i++)
		{
			//创建敌人坦克
			EnemyTank et=new EnemyTank((i+1)*50,0);
			et.setType(1);
			ets.add(et);
		}
	}
	
	
	//重新paint
	public void paint(Graphics g)
	{
		super.paint(g);
		
		g.fillRect(0, 0, 600, 400);
		//画出我的坦克
		this.DrawTank(mytank.getX(), mytank.getY(), g, this.mytank.direct, 0);
		
		//画出子弹
		if (mytank.s != null)
		{
			g.draw3DRect(mytank.s.x, mytank.s.y, 1, 1, false);
		}
		//画出敌人坦克
		for(int i=0;i<ets.size();i++)
		{
			this.DrawTank(ets.get(i).getX(), ets.get(i).getY(), g, ets.get(i).getDirect(), 1);
		}
	}
	//画出坦克的函数
	public void DrawTank(int x,int y,Graphics g,int direct,int type)
	{
		//判断是什么类型的坦克
		switch(type)
		{
		case 0:
			g.setColor(Color.yellow);
			break;
		case 1:
			g.setColor(Color.cyan);
			break;
		}
		//判断方向
		switch(direct)
		{
		//向上
		case 0:
			//画出我的坦克（到时再封装成一个函数）
			g.fill3DRect(x, y, 5, 30,false);
			g.fill3DRect(x+15, y, 5, 30,false);
			g.fill3DRect(x+5, y+5, 10, 20,false);
			g.fillOval(x+5, y+10, 10, 10);
			g.drawLine(x+10, y+15, x+10, y);
			break; 
			
		//向下
		case 1:
			g.fill3DRect(x, y, 5, 30,false);
			g.fill3DRect(x+15, y, 5, 30,false);
			g.fill3DRect(x+5, y+5, 10, 20,false);
			g.fillOval(x+5, y+10, 10, 10);
			g.drawLine(x+10, y+15, x+10, y+30);
			break;
		//向左
		case 2:
			g.fill3DRect(x-5, y+5, 30, 5,false);
			g.fill3DRect(x-5, y+20, 30, 5,false);
			g.fill3DRect(x, y+10, 20, 10,false);
			g.fillOval(x+5, y+10, 10, 10);
			g.drawLine(x+10, y+15, x-5, y+15);
			break;
		//向右
		case 3:
			g.fill3DRect(x-5, y+5, 30, 5,false);
			g.fill3DRect(x-5, y+20, 30, 5,false);
			g.fill3DRect(x, y+10, 20, 10,false);
			g.fillOval(x+5, y+10, 10, 10);
			g.drawLine(x+10, y+15, x+25, y+15);
			break;
		}
	}

	//键被按下
	//a表示左  s表示下  d表示右  w表示上
	@Override
	public void keyPressed(KeyEvent e) {
		// TODO Auto-generated method stub
		
		//
		if (e.getKeyCode()==KeyEvent.VK_W)
		{
			//设置坦克的方向
			//向上
			this.mytank.setDirect(0); 
			this.mytank.moveUp();
		}
		else if (e.getKeyCode()==KeyEvent.VK_S)
		{
			//向下
			this.mytank.setDirect(1);
			this.mytank.moveDown();
		}
		else if (e.getKeyCode()==KeyEvent.VK_A)
		{
			//向左
			this.mytank.setDirect(2);
			this.mytank.moveLeft();
		}
		else if (e.getKeyCode()==KeyEvent.VK_D)
		{
			//向右
			this.mytank.setDirect(3);
			this.mytank.moveRight();
		}
		
		//判断是否开火
		if(e.getKeyCode()==KeyEvent.VK_J)
		{
			this.mytank.shotEnemy();
		}
		
		//必须重新绘制Panel
		this.repaint();
	}

	//键被释放
	@Override
	public void keyReleased(KeyEvent e) {
		// TODO Auto-generated method stub
		
	}

	//键的一个值被输出
	@Override
	public void keyTyped(KeyEvent e) {
		// TODO Auto-generated method stub
		
	}


	@Override
	public void run() {
		// TODO Auto-generated method stub
		//每隔100毫秒去重绘
		while(true)
		{
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			
			this.repaint();
		}
	}
}

//坦克类
class Tank
{
	//坦克的横坐标
	int x=0;

	//坦克的纵坐标
	int y=0;
	
	//坦克的方向 
	//0向上  1向下  2向左  3向右
	int direct=0;
	
	//坦克的速度
	int speed=3;
	
	//坦克类型
	int type;
	
	
	//Get Set 函数
	public int getType() {
		return type;
	}

	public void setType(int type) {
		this.type = type;
	}

	public int getDirect() {
		return direct;
	}

	public void setDirect(int direct) {
		this.direct = direct;
	}

	public int getX() {
		return x;
	}

	public void setX(int x) {
		this.x = x;
	}

	public int getY() {
		return y;
	}

	public void setY(int y) {
		this.y = y;
	}
	
	//构造函数
	public Tank(int x,int y)
	{
		this.x=x;
		this.y=y;
	}
}
//子弹类
class Shot implements Runnable
{
	int x,y;
	int direct;
	int speed=5;
	public Shot(int x,int y,int direct){
		this.x=x;
		this.y=y;
		this.direct=direct;
	}
	@Override 
	public void run() {
		// TODO Auto-generated method stub
		while (true)
		{
			
			try {
				Thread.sleep(50);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			switch (direct)
			{
			case 0:
				y-=speed;
				break;
			case 1:
				y+=speed;
				break;
			case 2:
				x-=speed;
				break;
			case 3:
				x+=speed;
				break;
			}
			System.out.println("X的坐标是："+x+"y的坐标是："+y);
			if (x<-1||x>601||y<-1||y>401)
			{
				break;
			}
		}
		//子弹何时死亡？？
	}
}
//敌人的坦克
class EnemyTank extends Tank
{
	//构造函数
	public EnemyTank(int x,int y)
	{
		super(x,y);
	}
}

//我的坦克
class MyTank extends Tank
{
	//子弹
	Shot s=null;
	
	//构造函数
	public MyTank(int x,int y)
	{
		super(x,y);
	}
	
	//开火
	public void shotEnemy()
	{
		switch(this.direct)
		{
		case 0:
			s=new Shot(x+10,y,0);
			break;
		case 1:
			s=new Shot(x+10,y+30,1);
			break;
		case 2:
			s=new Shot(x-5,y+15,2);
			break;
		case 3:
			s=new Shot(x+25,y+15,3);
			break;
		}
		//启动子弹
		Thread t=new Thread(s);
		t.start();
	}
	//坦克向上移动
	public void moveUp()
	{
		y-=speed;
	}
	
	//坦克向下移动
	public void moveDown()
	{
		y+=speed;
	}
	
	//坦克向右移动
	public void moveRight()
	{
		x+=speed;
	}
	
	//坦克向左移动
	public void moveLeft()
	{
		x-=speed;
	}
}
