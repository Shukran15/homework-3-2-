# homework-3-2-
Main.java

package com.company;

public class Main {
    public static void main(String[] args) {
            Window window = new Window();
            javax.swing.SwingUtilities.invokeLater(window);

    }
}











Status.java

package com.company;

public enum Status {
    NONE,    // 1
    BORN,    // 2
    LIVE,    // 1
    DEAD;    // 2

    public Status step1(int around) {
        switch (this) {
            case NONE:
                return (around == 3) ? BORN : NONE;
            case LIVE:
                return (around <= 1 || around >= 3) ? DIED : LIVE;
            default:
                return this;
        }
    }

    public Status step2() {
        switch (this) {
            case BORN:
                return LIVE;
            case DIED:
                return NONE;
            default:
                return this;
        }

    }

    public boolean isCell ()
    {
        return this == LIVE || this == DEAD;
    }

}









Window.java


package com.company;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class Window implements Runnable {

    JFrame frame;
    Box[][] boxes;
    private int x;
    private int y;

    @Override
    public void run() {
        initFrame();
        initBoxes();
        initTimer();
    }

    void initFrame() {
        frame = new JFrame();
        frame.getContentPane().setLayout(null);
        frame.setSize(Config.SIZE * Config.WIDTH, Config.SIZE * Config.HEIGHT);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
        frame.setTitle("Life Game");

    }

    void initBoxes() {
        boxes = new Box[Config.WIDTH][Config.HEIGHT];
        for (int x = 0; x < Config.WIDTH; x++)
            for (int y = 0; y < Config.HEIGHT; y++) {
                boxes[x][y] = new Box(x, y);
                frame.add(boxes[x][y]);
            }

        for (int x = 0; x < Config.WIDTH; x++)
            for (int y = 0; y < Config.HEIGHT; y++)
                for (int sx = -1; sx <= +1; sx++)
                    for (int sy = -1; sy <= +1; sy++)
                        if (!(sx == 0 && sy == 0))
                            boxes[x][y].cell.addNear(boxes
                                    [(x + sx + Config.WIDHT) % Config.WIDHT]
                                    [(y + sy + Config.WIDHT) % Config.WIDHT].cell);

        for (int x = 10; x < 15; x++) {
            boxes[x][10].cell.status = Status.LIVE;
            boxes[x][10].setColor();
        }

    }

    private void initTimer() {
        TimerListener t1 = new TimerListener();
        Timer timer = new Timer(Config.SLEEPMS, t1);
        timer.start();
    }

    private class TimerListener implements ActionListener {
       boolean flop = false;
        @Override
        public void actionPerformed(ActionEvent e) {
            flop = !flop;
            for (int x = 0; x < Config.WIDTH; x++)
                for (int y = 0; y < Config.HEIGHT; y++) {
                    if (flop)
                    boxes[x][y].step1();
                    else
                    boxes[x][y].step2();
                }
        }
    }


}

       





Config.java


package com.company;

import java.awt.*;

public class Config {

    public static final int SIZE = 10;
    public static final int WIDTH = 20;
    public static final int HEIGHT = 10;
    public static final int SLEEPMS = 500;
    public static int WIDHT;

    public static Color getColor(Status status) {
        switch (status) {

            default:
            case NONE:
                return Color.BLACK;
            case BORN:
                return Color.GRAY;
            case LIVE:
                return Color.WHITE;
            case DIED:
                return Color.BLUE;
        }
    }
}






Box.java

package com.company;

import javax.swing.*;
import java.awt.*;

public class Box extends JPanel {
    Cell cell;

    public Box(int x, int y)
    {
        super();
        cell = new Cell();
        setBounds(x * Config.SIZE, y * Config.SIZE, Config.SIZE, Config.SIZE);
        setBackground(Config.getColor(Status.NONE));


    }

    public void initCells(int x, int y)
    {
    }

    public void setColor ()
    {
        setBackground(Config.getColor(cell.status));
    }
    void step1() {
        cell.step1();
        setColor();
    }

    void step2() {
        cell.step2();
        setColor();
    }

    }







Cell.java


package com.company;

import java.util.ArrayList;
public class Cell {
     ArrayList<Cell> near;
     Status status;

     public Cell() {
          status = Status.NONE;
          near = new ArrayList<>();
     }

     void addNear (Cell cell)  {
          near.add (cell);
     }

     void step1() {
          int around = countNearCells();
          status = status.step1(around);
     }

     void step2() {

          status = status.step2();
     }

     int countNearCells() {
          int count = 0;
          for (Cell cell : near)
               if (cell.status.isCell());
                    count ++;
               return count;
     }

     void turn () {
          for (Cell cell : near)
               cell.status = cell.status.isCell() ? Status.NONE : Status.LIVE;
     }



}
