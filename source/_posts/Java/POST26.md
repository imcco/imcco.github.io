---
title: java 获取音视频文件的大小、以及播放时长
tags:
  - Java
copyright: true
category: Java
abbrlink: 54695
date: 2017-12-14 13:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0015.jpg)

简介
The JAVE (Java Audio Video Encoder) library is Java wrapper on the ffmpeg project. Developers can take take advantage of JAVE to transcode audio and video files from a format to another. In example you can transcode an AVI file to a MPEG one, you can change a DivX video stream into a (youtube like) Flash FLV one, you can convert a WAV audio file to a MP3 or a Ogg Vorbis one, you can separate and transcode audio and video tracks, you can resize videos, changing their sizes and proportions and so on. Many other formats, containers and operations are supported by JAVE.
看介绍这么强大，其实我的需求只是想要获取视频、语音的长度而已。
<!--more-->
使用

        总共只有一个jar包，API也非常简单，不说了，贴代码


首先引入jave-1.0.2.jar，

写了个demo 仅供参考

```java
package com.readVideo.test;
 
 
public class VideoName {
 
    private int id;
    private String name;
    private String time;
    private String size;
    private String leaf;
    private String url;
    private String brief;
    private int VIDEO_DETAIL_ID;
 
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getTime() {
        return time;
    }
    public void setTime(String time) {
        this.time = time;
    }
    public String getSize() {
        return size;
    }
    public void setSize(String size) {
        this.size = size;
    }
    public String getLeaf() {
        return leaf;
    }
    public void setLeaf(String leaf) {
        this.leaf = leaf;
    }
    public String getUrl() {
        return url;
    }
    public void setUrl(String url) {
        this.url = url;
    }
    public String getBrief() {
        return brief;
    }
    public void setBrief(String brief) {
        this.brief = brief;
    }
    public int getVIDEO_DETAIL_ID() {
        return VIDEO_DETAIL_ID;
    }
    public void setVIDEO_DETAIL_ID(int vIDEO_DETAIL_ID) {
        VIDEO_DETAIL_ID = vIDEO_DETAIL_ID;
    }
     
     
}

```

```java
package com.readVideo.test;
 
import it.sauronsoftware.jave.Encoder;
import it.sauronsoftware.jave.MultimediaInfo;
import java.io.File;
import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;
 
public class ReadVideoMessages {
 
    /**
     * 得到视频的大小
     *
     * @param f
     *            文件
     * @return 视频的大小
     */
    public static String getFileSize(File f) {
        // 保留两位小数
        DecimalFormat df = new DecimalFormat(".##");
        // 得到视频的长度
        Long long1 = f.length();
        String size = "";
        long G = 1024 * 1024 * 1024;
        long M = 1024 * 1024;
        long K = 1024;
        // 视频大小超过G、超过M不超过G、超过K不超过M
        if (long1 / G >= 1) {
            size = df.format((double) long1 / G) + "G";
        } else if (long1 / M >= 1) {
            size = df.format((double) long1 / M) + "M";
        } else if (long1 / K >= 1) {
            size = df.format((double) long1 / K) + "K";
        } else {
            size = long1 + "B";
        }
        // System.out.println(time);
        return size;
 
    }
 
    /**
     * 得到视频的长度
     *
     * @param f
     *            文件
     * @return 视频的长度
     */
    public static String getVideoTime(File f) {
        String time = "";
        //新建编码器对象
        Encoder encoder = new Encoder();
        try {
            //得到多媒体视频的信息
            MultimediaInfo m = encoder.getInfo(f);
            //得到毫秒级别的多媒体是视频长度
            long ls = m.getDuration();
            //转换为分秒
            time = ls / 60000 + "分" + (ls - (ls / 60000 * 60000)) / 1000 + "秒";
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return time;
 
    }
 
    // 显示目录的方法
    /**
     * 得到视频所有的信息
     *
     * @param file
     *            文件夹 or 文件
     * @return 视频的信息
     */
    public static List<VideoName> getAllMessage(File file) {
        List<VideoName> videoNames = new ArrayList<VideoName>();
        // System.out.println( file.getAbsolutePath());
        String time = "";
        String size = "";
        // 判断传入对象是否为一个文件夹对象
        if (!file.isDirectory()) {
            System.out.println("你输入的不是一个文件夹，请检查路径是否有误！！");
        } else {
            File[] f = file.listFiles();
            for (int i = 0; i < f.length; i++) {
                // 判断文件列表中的对象是否为文件夹对象，如果是则执行tree递归，直到把此文件夹中所有文件输出为止
                if (f[i].isDirectory()) {
                    System.out.println(f[i].getName() + "\tttdir");
                    // getAllMessage(f[i]);
                } else {
                    time = getVideoTime(f[i]);
                    if (time.equals("")) {
                        time = "未知";
                    }
                    size = getFileSize(f[i]);
                    VideoName videoName = new VideoName();
                    int j = f[i].getName().indexOf("-", 1);
                    videoName.setName(f[i].getName().substring(0, j));
                    videoName.setSize(size);
                    videoName.setTime(time);
                    videoName.setBrief(f[i].getName().substring(j + 1,
                            f[i].getName().length()));
                    videoName.setUrl("F:/Resource/video/硬件/AltiumDesigner/"
                            + f[i].getName());
                    videoNames.add(videoName);
                    System.out.println(time
                            + "---"
                            + size
                            + "---"
                            + f[i].getName().substring(0, j)
                            + "---"
                            + f[i].getName().substring(j + 1,
                                    f[i].getName().length()) + "---"
                            + "F:/Resource/video/硬件/AltiumDesigner/"
                            + f[i].getName());
                }
            }
        }
        return videoNames;
 
    }
 
    public static void main(String[] args) {
        File f = new File("F:\\Resource\\video\\软件\\LabView");
        List<VideoName> videoMessages = getAllMessage(f);
        System.out.println(videoMessages.size());
    }
 
}

```
