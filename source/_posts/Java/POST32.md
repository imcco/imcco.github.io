---
title: Java工具类总结
tags: Java
category: Java
abbrlink: 36591
date: 2018-01-16 23:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0091.jpg)

Java FileUtils、StringUtils和DateUtils笔记 
<!--more-->
# FileUtils

## 我项目中用到的文件工具类
1. 读取raw文件、file文件，drawable文件，asset文件，比如本地的json数据，本地文本等； 
如：String result =FileUtil.getString(context,”raw://first.json”) 
2. 读取本地的property文件，并转化为hashMap类型的数据 （simpleProperty2HashMap）； 
3. 将raw文件拷贝到指定目录（copyRawFile）； 
4. 基本文件读写操作（readFile，writeFile）； 
5. 从文件的完整路径名（路径+文件名）中提取 路径（extractFilePath）； 
6. 从文件的完整路径名（路径+文件名）中提取文件名(包含扩展名) 
如：d:\path\file.ext –> file.ext（extractFileName） 
7. 检查指定文件的路径是否存在（pathExists） 
8. 检查制定文件是否存在（fileExists） 
9. 创建目录（makeDir） 
10. 移除字符串中的BOM前缀（removeBomHeaderIfExists）


```java
package com.nsu.edu.library.utils;

import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.drawable.BitmapDrawable;
import android.text.TextUtils;

import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Properties;
import java.util.Set;

/**
 * Create By Anthony on 2016/1/15
 * Class Note:文件工具类
 * 包含内容：
 * 1 读取raw文件、file文件，drawable文件，asset文件，比如本地的json数据，本地文本等；
 *  如：String result =FileUtil.getString(context,"raw://first.json")
 * 2 读取本地的property文件，并转化为hashMap类型的数据（simpleProperty2HashMap）；
 * 3 将raw文件拷贝到指定目录（copyRawFile）；
 * 4 基本文件读写操作（readFile，writeFile）；
 * 5 从文件的完整路径名（路径+文件名）中提取 路径（extractFilePath）；
 * 6    从文件的完整路径名（路径+文件名）中提取文件名(包含扩展名) 
   如：d:\path\file.ext --> file.ext（extractFileName）
 *7 检查指定文件的路径是否存在（pathExists）
 *8 检查制定文件是否存在（fileExists）
 *9 创建目录（makeDir）
 *10 移除字符串中的BOM前缀（removeBomHeaderIfExists）
 */
public class FileUtil {
    public static final String ASSETS_PREFIX = "file://android_assets/";
    public static final String ASSETS_PREFIX2 = "file://android_asset/";
    public static final String ASSETS_PREFIX3 = "assets://";
    public static final String ASSETS_PREFIX4 = "asset://";
    public static final String RAW_PREFIX = "file://android_raw/";
    public static final String RAW_PREFIX2 = "raw://";
    public static final String FILE_PREFIX = "file://";
    public static final String DRAWABLE_PREFIX = "drawable://";

    public static InputStream getStream(Context context, String url) throws IOException {
        String lowerUrl = url.toLowerCase();
        InputStream is;
        if (lowerUrl.startsWith(ASSETS_PREFIX)) {
            String assetPath = url.substring(ASSETS_PREFIX.length());
            is = getAssetsStream(context, assetPath);
        } else if (lowerUrl.startsWith(ASSETS_PREFIX2)) {
            String assetPath = url.substring(ASSETS_PREFIX2.length());
            is = getAssetsStream(context, assetPath);
        } else if (lowerUrl.startsWith(ASSETS_PREFIX3)) {
            String assetPath = url.substring(ASSETS_PREFIX3.length());
            is = getAssetsStream(context, assetPath);
        } else if (lowerUrl.startsWith(ASSETS_PREFIX4)) {
            String assetPath = url.substring(ASSETS_PREFIX4.length());
            is = getAssetsStream(context, assetPath);
        } else if (lowerUrl.startsWith(RAW_PREFIX)) {
            String rawName = url.substring(RAW_PREFIX.length());
            is = getRawStream(context, rawName);
        } else if (lowerUrl.startsWith(RAW_PREFIX2)) {
            String rawName = url.substring(RAW_PREFIX2.length());
            is = getRawStream(context, rawName);
        } else if (lowerUrl.startsWith(FILE_PREFIX)) {
            String filePath = url.substring(FILE_PREFIX.length());
            is = getFileStream(filePath);
        } else if (lowerUrl.startsWith(DRAWABLE_PREFIX)) {
            String drawableName = url.substring(DRAWABLE_PREFIX.length());
            is = getDrawableStream(context, drawableName);
        } else {
            throw new IllegalArgumentException(String.format("Unsupported url: %s \n" +
                    "Supported: \n%sxxx\n%sxxx\n%sxxx", url, ASSETS_PREFIX, RAW_PREFIX, FILE_PREFIX));
        }
        return is;
    }

    private static InputStream getAssetsStream(Context context, String path) throws IOException {
        return context.getAssets().open(path);
    }

    private static InputStream getFileStream(String path) throws IOException {
        return new FileInputStream(path);
    }

    private static InputStream getRawStream(Context context, String rawName) throws IOException {
        int id = context.getResources().getIdentifier(rawName, "raw", context.getPackageName());
        if (id != 0) {
            try {
                return context.getResources().openRawResource(id);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        throw new IOException(String.format("raw of id: %s from %s not found", id, rawName));
    }

    private static InputStream getDrawableStream(Context context, String rawName) throws IOException {
        int id = context.getResources().getIdentifier(rawName, "drawable", context.getPackageName());
        if (id != 0) {
            BitmapDrawable drawable = (BitmapDrawable) context.getResources().getDrawable(id);
            Bitmap bitmap = drawable.getBitmap();

            ByteArrayOutputStream os = new ByteArrayOutputStream();
            bitmap.compress(Bitmap.CompressFormat.PNG, 0, os);
            return new ByteArrayInputStream(os.toByteArray());
        }

        throw new IOException(String.format("bitmap of id: %s from %s not found", id, rawName));
    }

    public static String getString(Context context, String url) throws IOException {
        return getString(context, url, "UTF-8");
    }

    public static String getString(Context context, String url, String encoding) throws IOException {
        String result = readStreamString(getStream(context, url), encoding);
        if (result.startsWith("\ufeff")) {
            result = result.substring(1);
        }

        return result;
    }

    public static String readStreamString(InputStream is, String encoding) throws IOException {
        return new String(readStream(is), encoding);
    }

    public static byte[] readStream(InputStream is) throws IOException {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buf = new byte[1024 * 10];
        int readlen;
        while ((readlen = is.read(buf)) >= 0) {
            baos.write(buf, 0, readlen);
        }
        baos.close();

        return baos.toByteArray();
    }

    public static Bitmap getDrawableBitmap(Context context, String rawName) {
        int id = context.getResources().getIdentifier(rawName, "drawable", context.getPackageName());
        if (id != 0) {
            BitmapDrawable drawable = (BitmapDrawable) context.getResources().getDrawable(id);
            if (drawable != null) {
                return drawable.getBitmap();
            }
        }

        return null;
    }

    /**
     * 读取Property文件
     */
    public static HashMap<String, String> simpleProperty2HashMap(Context context, String path) {
        try {
            InputStream is = getStream(context, path);
            return simpleProperty2HashMap(is);
        } catch (IOException e) {
            e.printStackTrace();
        }

        return new HashMap<String, String>();
    }

    private static HashMap<String, String> simpleProperty2HashMap(InputStream in) throws IOException {
        HashMap<String, String> hashMap = new HashMap<String, String>();
        Properties properties = new Properties();
        properties.load(in);
        in.close();
        Set keyValue = properties.keySet();
        for (Iterator it = keyValue.iterator(); it.hasNext(); ) {
            String key = (String) it.next();
            hashMap.put(key, (String) properties.get(key));
        }

        return hashMap;
    }

    /**
     * 将raw文件拷贝到指定目录
     */
    public static void copyRawFile(Context ctx, String rawFileName, String to) {
        String[] names = rawFileName.split("\\.");
        String toFile = to + "/" + names[0] + "." + names[1];
        File file = new File(toFile);
        if (file.exists()) {
            return;
        }
        try {
            InputStream is = getStream(ctx, "raw://" + names[0]);
            OutputStream os = new FileOutputStream(toFile);
            int byteCount = 0;
            byte[] bytes = new byte[1024];

            while ((byteCount = is.read(bytes)) != -1) {
                os.write(bytes, 0, byteCount);
            }
            os.close();
            is.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 基本文件操作
     */
    public static String FILE_READING_ENCODING = "UTF-8";
    public static String FILE_WRITING_ENCODING = "UTF-8";

    public static String readFile(String _sFileName, String _sEncoding) throws Exception {
        StringBuffer buffContent = null;
        String sLine;

        FileInputStream fis = null;
        BufferedReader buffReader = null;
        if (_sEncoding == null || "".equals(_sEncoding)) {
            _sEncoding = FILE_READING_ENCODING;
        }

        try {
            fis = new FileInputStream(_sFileName);
            buffReader = new BufferedReader(new InputStreamReader(fis,
                    _sEncoding));
            boolean zFirstLine = "UTF-8".equalsIgnoreCase(_sEncoding);
            while ((sLine = buffReader.readLine()) != null) {
                if (buffContent == null) {
                    buffContent = new StringBuffer();
                } else {
                    buffContent.append("\n");
                }
                if (zFirstLine) {
                    sLine = removeBomHeaderIfExists(sLine);
                    zFirstLine = false;
                }
                buffContent.append(sLine);
            }// end while
            return (buffContent == null ? "" : buffContent.toString());
        } catch (FileNotFoundException ex) {
            throw new Exception("要读取的文件没有找到!", ex);
        } catch (IOException ex) {
            throw new Exception("读取文件时错误!", ex);
        } finally {
            // 增加异常时资源的释放
            try {
                if (buffReader != null)
                    buffReader.close();
                if (fis != null)
                    fis.close();
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }

    public static File writeFile(InputStream is, String path, boolean isOverride) throws Exception {
        String sPath = extractFilePath(path);
        if (!pathExists(sPath)) {
            makeDir(sPath, true);
        }

        if (!isOverride && fileExists(path)) {
            if(path.contains(".")) {
                String suffix = path.substring(path.lastIndexOf("."));
                String pre = path.substring(0, path.lastIndexOf("."));
                path = pre + "_" + TimeUtils.getNowTime() + suffix;
            } else {
                path = path + "_" + TimeUtils.getNowTime();
            }
        }

        FileOutputStream os = null;
        File file = null;

        try {
            file = new File(path);
            os = new FileOutputStream(file);
            int byteCount = 0;
            byte[] bytes = new byte[1024];

            while ((byteCount = is.read(bytes)) != -1) {
                os.write(bytes, 0, byteCount);
            }
            os.flush();

            return file;
        } catch (Exception e) {
            e.printStackTrace();
            throw new Exception("写文件错误", e);
        } finally {
            try {
                if (os != null)
                    os.close();
                if (is != null)
                    is.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public static File writeFile(String path, String content, String encoding, boolean isOverride) throws Exception {
        if (TextUtils.isEmpty(encoding)) {
            encoding = FILE_WRITING_ENCODING;
        }
        InputStream is = new ByteArrayInputStream(content.getBytes(encoding));
        return writeFile(is, path, isOverride);
    }

    /**
     * 从文件的完整路径名（路径+文件名）中提取 路径（包括：Drive+Directroy )
     *
     * @param _sFilePathName
     * @return
     */
    public static String extractFilePath(String _sFilePathName) {
        int nPos = _sFilePathName.lastIndexOf('/');
        if (nPos < 0) {
            nPos = _sFilePathName.lastIndexOf('\\');
        }

        return (nPos >= 0 ? _sFilePathName.substring(0, nPos + 1) : "");
    }

    /**
     * 从文件的完整路径名（路径+文件名）中提取文件名(包含扩展名) <br>
     * 如：d:\path\file.ext --> file.ext
     *
     * @param _sFilePathName
     * @return
     */
    public static String extractFileName(String _sFilePathName) {
        return extractFileName(_sFilePathName, File.separator);
    }

    /**
     * 从文件的完整路径名（路径+文件名）中提取文件名(包含扩展名) <br>
     * 如：d:\path\file.ext --> file.ext
     *
     * @param _sFilePathName  全文件路径名
     * @param _sFileSeparator 文件分隔符
     * @return
     */
    public static String extractFileName(String _sFilePathName,
                                         String _sFileSeparator) {
        int nPos = -1;
        if (_sFileSeparator == null) {
            nPos = _sFilePathName.lastIndexOf(File.separatorChar);
            if (nPos < 0) {
                nPos = _sFilePathName
                        .lastIndexOf(File.separatorChar == '/' ? '\\' : '/');
            }
        } else {
            nPos = _sFilePathName.lastIndexOf(_sFileSeparator);
        }

        if (nPos < 0) {
            return _sFilePathName;
        }

        return _sFilePathName.substring(nPos + 1);
    }

    /**
     * 检查指定文件的路径是否存在
     *
     * @param _sPathFileName 文件名称(含路径）
     * @return 若存在，则返回true；否则，返回false
     */
    public static boolean pathExists(String _sPathFileName) {
        String sPath = extractFilePath(_sPathFileName);
        return fileExists(sPath);
    }

    public static boolean fileExists(String _sPathFileName) {
        File file = new File(_sPathFileName);
        return file.exists();
    }

    /**
     * 创建目录
     *
     * @param _sDir             目录名称
     * @param _bCreateParentDir 如果父目录不存在，是否创建父目录
     * @return
     */
    public static boolean makeDir(String _sDir, boolean _bCreateParentDir) {
        boolean zResult = false;
        File file = new File(_sDir);
        if (_bCreateParentDir)
            zResult = file.mkdirs(); // 如果父目录不存在，则创建所有必需的父目录
        else
            zResult = file.mkdir(); // 如果父目录不存在，不做处理
        if (!zResult)
            zResult = file.exists();
        return zResult;
    }

    /**
     * 移除字符串中的BOM前缀
     *
     * @param _sLine 需要处理的字符串
     * @return 移除BOM后的字符串.
     */
    private static String removeBomHeaderIfExists(String _sLine) {
        if (_sLine == null) {
            return null;
        }
        String line = _sLine;
        if (line.length() > 0) {
            char ch = line.charAt(0);
            // 使用while是因为用一些工具看到过某些文件前几个字节都是0xfffe.
            // 0xfeff,0xfffe是字节序的不同处理.JVM中,一般是0xfeff
            while ((ch == 0xfeff || ch == 0xfffe)) {
                line = line.substring(1);
                if (line.length() == 0) {
                    break;
                }
                ch = line.charAt(0);
            }
        }
        return line;
    }

}

```
## 网上的工具类
这个工具类也大同小异。其中也有很多和我上面重复的一些方法，也有上面没有的方法.


```java
package com.nsu.edu.library.utils;

import android.text.TextUtils;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.util.ArrayList;
import java.util.List;

/**
 * File Utils
 * <ul>
 * Read or write file
 * <li>{@link #readFile(String, String)} read file</li>
 * <li>{@link #readFileToList(String, String)} read file to string list</li>
 * <li>{@link #writeFile(String, String, boolean)} write file from String</li>
 * <li>{@link #writeFile(String, String)} write file from String</li>
 * <li>{@link #writeFile(String, List, boolean)} write file from String List</li>
 * <li>{@link #writeFile(String, List)} write file from String List</li>
 * <li>{@link #writeFile(String, InputStream)} write file</li>
 * <li>{@link #writeFile(String, InputStream, boolean)} write file</li>
 * <li>{@link #writeFile(File, InputStream)} write file</li>
 * <li>{@link #writeFile(File, InputStream, boolean)} write file</li>
 * </ul>
 * <ul>
 * Operate file
 * <li>{@link #moveFile(File, File)} or {@link #moveFile(String, String)}</li>
 * <li>{@link #copyFile(String, String)}</li>
 * <li>{@link #getFileExtension(String)}</li>
 * <li>{@link #getFileName(String)}</li>
 * <li>{@link #getFileNameWithoutExtension(String)}</li>
 * <li>{@link #getFileSize(String)}</li>
 * <li>{@link #deleteFile(String)}</li>
 * <li>{@link #isFileExist(String)}</li>
 * <li>{@link #isFolderExist(String)}</li>
 * <li>{@link #makeFolders(String)}</li>
 * <li>{@link #makeDirs(String)}</li>
 * </ul>
 * 
 * @author <a href="http://www.trinea.cn" target="_blank">Trinea</a> 2012-5-12
 */
public class FileUtils {

    public final static String FILE_EXTENSION_SEPARATOR = ".";

    private FileUtils() {
        throw new AssertionError();
    }

    /**
     * read file
     * 
     * @param filePath
     * @param charsetName The name of a supported {@link java.nio.charset.Charset </code>charset<code>}
     * @return if file not exist, return null, else return content of file
     * @throws RuntimeException if an error occurs while operator BufferedReader
     */
    public static StringBuilder readFile(String filePath, String charsetName) {
        File file = new File(filePath);
        StringBuilder fileContent = new StringBuilder("");
        if (file == null || !file.isFile()) {
            return null;
        }

        BufferedReader reader = null;
        try {
            InputStreamReader is = new InputStreamReader(new FileInputStream(file), charsetName);
            reader = new BufferedReader(is);
            String line = null;
            while ((line = reader.readLine()) != null) {
                if (!fileContent.toString().equals("")) {
                    fileContent.append("\r\n");
                }
                fileContent.append(line);
            }
            return fileContent;
        } catch (IOException e) {
            throw new RuntimeException("IOException occurred. ", e);
        } finally {
            IOUtils.close(reader);
        }
    }

    /**
     * write file
     * 
     * @param filePath
     * @param content
     * @param append is append, if true, write to the end of file, else clear content of file and write into it
     * @return return false if content is empty, true otherwise
     * @throws RuntimeException if an error occurs while operator FileWriter
     */
    public static boolean writeFile(String filePath, String content, boolean append) {
        if (StringUtils.isEmpty(content)) {
            return false;
        }

        FileWriter fileWriter = null;
        try {
            makeDirs(filePath);
            fileWriter = new FileWriter(filePath, append);
            fileWriter.write(content);
            return true;
        } catch (IOException e) {
            throw new RuntimeException("IOException occurred. ", e);
        } finally {
            IOUtils.close(fileWriter);
        }
    }

    /**
     * write file
     * 
     * @param filePath
     * @param contentList
     * @param append is append, if true, write to the end of file, else clear content of file and write into it
     * @return return false if contentList is empty, true otherwise
     * @throws RuntimeException if an error occurs while operator FileWriter
     */
    public static boolean writeFile(String filePath, List<String> contentList, boolean append) {
        if (ListUtils.isEmpty(contentList)) {
            return false;
        }

        FileWriter fileWriter = null;
        try {
            makeDirs(filePath);
            fileWriter = new FileWriter(filePath, append);
            int i = 0;
            for (String line : contentList) {
                if (i++ > 0) {
                    fileWriter.write("\r\n");
                }
                fileWriter.write(line);
            }
            return true;
        } catch (IOException e) {
            throw new RuntimeException("IOException occurred. ", e);
        } finally {
            IOUtils.close(fileWriter);
        }
    }

    /**
     * write file, the string will be written to the begin of the file
     * 
     * @param filePath
     * @param content
     * @return
     */
    public static boolean writeFile(String filePath, String content) {
        return writeFile(filePath, content, false);
    }

    /**
     * write file, the string list will be written to the begin of the file
     * 
     * @param filePath
     * @param contentList
     * @return
     */
    public static boolean writeFile(String filePath, List<String> contentList) {
        return writeFile(filePath, contentList, false);
    }

    /**
     * write file, the bytes will be written to the begin of the file
     * 
     * @param filePath
     * @param stream
     * @return
     * @see {@link #writeFile(String, InputStream, boolean)}
     */
    public static boolean writeFile(String filePath, InputStream stream) {
        return writeFile(filePath, stream, false);
    }

    /**
     * write file
     * 
     * @param file the file to be opened for writing.
     * @param stream the input stream
     * @param append if <code>true</code>, then bytes will be written to the end of the file rather than the beginning
     * @return return true
     * @throws RuntimeException if an error occurs while operator FileOutputStream
     */
    public static boolean writeFile(String filePath, InputStream stream, boolean append) {
        return writeFile(filePath != null ? new File(filePath) : null, stream, append);
    }

    /**
     * write file, the bytes will be written to the begin of the file
     * 
     * @param file
     * @param stream
     * @return
     * @see {@link #writeFile(File, InputStream, boolean)}
     */
    public static boolean writeFile(File file, InputStream stream) {
        return writeFile(file, stream, false);
    }

    /**
     * write file
     * 
     * @param file the file to be opened for writing.
     * @param stream the input stream
     * @param append if <code>true</code>, then bytes will be written to the end of the file rather than the beginning
     * @return return true
     * @throws RuntimeException if an error occurs while operator FileOutputStream
     */
    public static boolean writeFile(File file, InputStream stream, boolean append) {
        OutputStream o = null;
        try {
            makeDirs(file.getAbsolutePath());
            o = new FileOutputStream(file, append);
            byte data[] = new byte[1024];
            int length = -1;
            while ((length = stream.read(data)) != -1) {
                o.write(data, 0, length);
            }
            o.flush();
            return true;
        } catch (FileNotFoundException e) {
            throw new RuntimeException("FileNotFoundException occurred. ", e);
        } catch (IOException e) {
            throw new RuntimeException("IOException occurred. ", e);
        } finally {
            IOUtils.close(o);
            IOUtils.close(stream);
        }
    }

    /**
     * move file
     * 
     * @param sourceFilePath
     * @param destFilePath
     */
    public static void moveFile(String sourceFilePath, String destFilePath) {
        if (TextUtils.isEmpty(sourceFilePath) || TextUtils.isEmpty(destFilePath)) {
            throw new RuntimeException("Both sourceFilePath and destFilePath cannot be null.");
        }
        moveFile(new File(sourceFilePath), new File(destFilePath));
    }

    /**
     * move file
     * 
     * @param srcFile
     * @param destFile
     */
    public static void moveFile(File srcFile, File destFile) {
        boolean rename = srcFile.renameTo(destFile);
        if (!rename) {
            copyFile(srcFile.getAbsolutePath(), destFile.getAbsolutePath());
            deleteFile(srcFile.getAbsolutePath());
        }
    }

    /**
     * copy file
     * 
     * @param sourceFilePath
     * @param destFilePath
     * @return
     * @throws RuntimeException if an error occurs while operator FileOutputStream
     */
    public static boolean copyFile(String sourceFilePath, String destFilePath) {
        InputStream inputStream = null;
        try {
            inputStream = new FileInputStream(sourceFilePath);
        } catch (FileNotFoundException e) {
            throw new RuntimeException("FileNotFoundException occurred. ", e);
        }
        return writeFile(destFilePath, inputStream);
    }

    /**
     * read file to string list, a element of list is a line
     * 
     * @param filePath
     * @param charsetName The name of a supported {@link java.nio.charset.Charset </code>charset<code>}
     * @return if file not exist, return null, else return content of file
     * @throws RuntimeException if an error occurs while operator BufferedReader
     */
    public static List<String> readFileToList(String filePath, String charsetName) {
        File file = new File(filePath);
        List<String> fileContent = new ArrayList<String>();
        if (file == null || !file.isFile()) {
            return null;
        }

        BufferedReader reader = null;
        try {
            InputStreamReader is = new InputStreamReader(new FileInputStream(file), charsetName);
            reader = new BufferedReader(is);
            String line = null;
            while ((line = reader.readLine()) != null) {
                fileContent.add(line);
            }
            return fileContent;
        } catch (IOException e) {
            throw new RuntimeException("IOException occurred. ", e);
        } finally {
            IOUtils.close(reader);
        }
    }

    /**
     * get file name from path, not include suffix
     * 
     * <pre>
     *      getFileNameWithoutExtension(null)               =   null
     *      getFileNameWithoutExtension("")                 =   ""
     *      getFileNameWithoutExtension("   ")              =   "   "
     *      getFileNameWithoutExtension("abc")              =   "abc"
     *      getFileNameWithoutExtension("a.mp3")            =   "a"
     *      getFileNameWithoutExtension("a.b.rmvb")         =   "a.b"
     *      getFileNameWithoutExtension("c:\\")              =   ""
     *      getFileNameWithoutExtension("c:\\a")             =   "a"
     *      getFileNameWithoutExtension("c:\\a.b")           =   "a"
     *      getFileNameWithoutExtension("c:a.txt\\a")        =   "a"
     *      getFileNameWithoutExtension("/home/admin")      =   "admin"
     *      getFileNameWithoutExtension("/home/admin/a.txt/b.mp3")  =   "b"
     * </pre>
     * 
     * @param filePath
     * @return file name from path, not include suffix
     * @see
     */
    public static String getFileNameWithoutExtension(String filePath) {
        if (StringUtils.isEmpty(filePath)) {
            return filePath;
        }

        int extenPosi = filePath.lastIndexOf(FILE_EXTENSION_SEPARATOR);
        int filePosi = filePath.lastIndexOf(File.separator);
        if (filePosi == -1) {
            return (extenPosi == -1 ? filePath : filePath.substring(0, extenPosi));
        }
        if (extenPosi == -1) {
            return filePath.substring(filePosi + 1);
        }
        return (filePosi < extenPosi ? filePath.substring(filePosi + 1, extenPosi) : filePath.substring(filePosi + 1));
    }

    /**
     * get file name from path, include suffix
     * 
     * <pre>
     *      getFileName(null)               =   null
     *      getFileName("")                 =   ""
     *      getFileName("   ")              =   "   "
     *      getFileName("a.mp3")            =   "a.mp3"
     *      getFileName("a.b.rmvb")         =   "a.b.rmvb"
     *      getFileName("abc")              =   "abc"
     *      getFileName("c:\\")              =   ""
     *      getFileName("c:\\a")             =   "a"
     *      getFileName("c:\\a.b")           =   "a.b"
     *      getFileName("c:a.txt\\a")        =   "a"
     *      getFileName("/home/admin")      =   "admin"
     *      getFileName("/home/admin/a.txt/b.mp3")  =   "b.mp3"
     * </pre>
     * 
     * @param filePath
     * @return file name from path, include suffix
     */
    public static String getFileName(String filePath) {
        if (StringUtils.isEmpty(filePath)) {
            return filePath;
        }

        int filePosi = filePath.lastIndexOf(File.separator);
        return (filePosi == -1) ? filePath : filePath.substring(filePosi + 1);
    }

    /**
     * get folder name from path
     * 
     * <pre>
     *      getFolderName(null)               =   null
     *      getFolderName("")                 =   ""
     *      getFolderName("   ")              =   ""
     *      getFolderName("a.mp3")            =   ""
     *      getFolderName("a.b.rmvb")         =   ""
     *      getFolderName("abc")              =   ""
     *      getFolderName("c:\\")              =   "c:"
     *      getFolderName("c:\\a")             =   "c:"
     *      getFolderName("c:\\a.b")           =   "c:"
     *      getFolderName("c:a.txt\\a")        =   "c:a.txt"
     *      getFolderName("c:a\\b\\c\\d.txt")    =   "c:a\\b\\c"
     *      getFolderName("/home/admin")      =   "/home"
     *      getFolderName("/home/admin/a.txt/b.mp3")  =   "/home/admin/a.txt"
     * </pre>
     * 
     * @param filePath
     * @return
     */
    public static String getFolderName(String filePath) {

        if (StringUtils.isEmpty(filePath)) {
            return filePath;
        }

        int filePosi = filePath.lastIndexOf(File.separator);
        return (filePosi == -1) ? "" : filePath.substring(0, filePosi);
    }

    /**
     * get suffix of file from path
     * 
     * <pre>
     *      getFileExtension(null)               =   ""
     *      getFileExtension("")                 =   ""
     *      getFileExtension("   ")              =   "   "
     *      getFileExtension("a.mp3")            =   "mp3"
     *      getFileExtension("a.b.rmvb")         =   "rmvb"
     *      getFileExtension("abc")              =   ""
     *      getFileExtension("c:\\")              =   ""
     *      getFileExtension("c:\\a")             =   ""
     *      getFileExtension("c:\\a.b")           =   "b"
     *      getFileExtension("c:a.txt\\a")        =   ""
     *      getFileExtension("/home/admin")      =   ""
     *      getFileExtension("/home/admin/a.txt/b")  =   ""
     *      getFileExtension("/home/admin/a.txt/b.mp3")  =   "mp3"
     * </pre>
     * 
     * @param filePath
     * @return
     */
    public static String getFileExtension(String filePath) {
        if (StringUtils.isBlank(filePath)) {
            return filePath;
        }

        int extenPosi = filePath.lastIndexOf(FILE_EXTENSION_SEPARATOR);
        int filePosi = filePath.lastIndexOf(File.separator);
        if (extenPosi == -1) {
            return "";
        }
        return (filePosi >= extenPosi) ? "" : filePath.substring(extenPosi + 1);
    }

    /**
     * Creates the directory named by the trailing filename of this file, including the complete directory path required
     * to create this directory. <br/>
     * <br/>
     * <ul>
     * <strong>Attentions:</strong>
     * <li>makeDirs("C:\\Users\\Trinea") can only create users folder</li>
     * <li>makeFolder("C:\\Users\\Trinea\\") can create Trinea folder</li>
     * </ul>
     * 
     * @param filePath
     * @return true if the necessary directories have been created or the target directory already exists, false one of
     *         the directories can not be created.
     *         <ul>
     *         <li>if {@link FileUtils#getFolderName(String)} return null, return false</li>
     *         <li>if target directory already exists, return true</li>
     *         <li>return {@link File#makeFolder}</li>
     *         </ul>
     */
    public static boolean makeDirs(String filePath) {
        String folderName = getFolderName(filePath);
        if (StringUtils.isEmpty(folderName)) {
            return false;
        }

        File folder = new File(folderName);
        return (folder.exists() && folder.isDirectory()) ? true : folder.mkdirs();
    }

    /**
     * @param filePath
     * @return
     * @see #makeDirs(String)
     */
    public static boolean makeFolders(String filePath) {
        return makeDirs(filePath);
    }

    /**
     * Indicates if this file represents a file on the underlying file system.
     * 
     * @param filePath
     * @return
     */
    public static boolean isFileExist(String filePath) {
        if (StringUtils.isBlank(filePath)) {
            return false;
        }

        File file = new File(filePath);
        return (file.exists() && file.isFile());
    }

    /**
     * Indicates if this file represents a directory on the underlying file system.
     * 
     * @param directoryPath
     * @return
     */
    public static boolean isFolderExist(String directoryPath) {
        if (StringUtils.isBlank(directoryPath)) {
            return false;
        }

        File dire = new File(directoryPath);
        return (dire.exists() && dire.isDirectory());
    }

    /**
     * delete file or directory
     * <ul>
     * <li>if path is null or empty, return true</li>
     * <li>if path not exist, return true</li>
     * <li>if path exist, delete recursion. return true</li>
     * <ul>
     * 
     * @param path
     * @return
     */
    public static boolean deleteFile(String path) {
        if (StringUtils.isBlank(path)) {
            return true;
        }

        File file = new File(path);
        if (!file.exists()) {
            return true;
        }
        if (file.isFile()) {
            return file.delete();
        }
        if (!file.isDirectory()) {
            return false;
        }
        for (File f : file.listFiles()) {
            if (f.isFile()) {
                f.delete();
            } else if (f.isDirectory()) {
                deleteFile(f.getAbsolutePath());
            }
        }
        return file.delete();
    }

    /**
     * get file size
     * <ul>
     * <li>if path is null or empty, return -1</li>
     * <li>if path exist and it is a file, return file size, else return -1</li>
     * <ul>
     * 
     * @param path
     * @return returns the length of this file in bytes. returns -1 if the file does not exist.
     */
    public static long getFileSize(String path) {
        if (StringUtils.isBlank(path)) {
            return -1;
        }

        File file = new File(path);
        return (file.exists() && file.isFile() ? file.length() : -1);
    }
}

```

```java
public class FileUtil{
    
 private static String message;
    /**
     * 读取文本文件内容
     * @param filePathAndName 带有完整绝对路径的文件名
     * @param encoding 文本文件打开的编码方式
     * @return 返回文本文件的内容
     */
    public static String readTxt(String filePathAndName,String encoding) throws IOException{
     encoding = encoding.trim();
     StringBuffer str = new StringBuffer("");
     String st = "";
     try{
      FileInputStream fs = new FileInputStream(filePathAndName);
      InputStreamReader isr;
      if(encoding.equals("")){
       isr = new InputStreamReader(fs);
      }else{
       isr = new InputStreamReader(fs,encoding);
      }
      BufferedReader br = new BufferedReader(isr);
      try{
       String data = "";
       while((data = br.readLine())!=null){
         str.append(data+" "); 
       }
      }catch(Exception e){
       str.append(e.toString());
      }
      st = str.toString();
     }catch(IOException es){
      st = "";
     }
     return st;     
    }

    /**
     * 新建目录
     * @param folderPath 目录
     * @return 返回目录创建后的路径
     */
    public static String createFolder(String folderPath) {
        String txt = folderPath;
        try {
            java.io.File myFilePath = new java.io.File(txt);
            txt = folderPath;
            if (!myFilePath.exists()) {
                myFilePath.mkdir();
            }
        }
        catch (Exception e) {
            message = "创建目录操作出错";
        }
        return txt;
    }
    
    /**
     * 多级目录创建
     * @param folderPath 准备要在本级目录下创建新目录的目录路径 例如 c:myf
     * @param paths 无限级目录参数，各级目录以单数线区分 例如 a|b|c
     * @return 返回创建文件后的路径 例如 c:myfac
     */
    public static String createFolders(String folderPath, String paths){
        String txts = folderPath;
        try{
            String txt;
            txts = folderPath;
            StringTokenizer st = new StringTokenizer(paths,"|");
            for(int i=0; st.hasMoreTokens(); i++){
                    txt = st.nextToken().trim();
                    if(txts.lastIndexOf("/")!=-1){ 
                        txts = createFolder(txts+txt);
                    }else{
                        txts = createFolder(txts+txt+"/");    
                    }
            }
       }catch(Exception e){
           message = "创建目录操作出错！";
       }
        return txts;
    }

    
    /**
     * 新建文件
     * @param filePathAndName 文本文件完整绝对路径及文件名
     * @param fileContent 文本文件内容
     * @return
     */
    public static void createFile(String filePathAndName, String fileContent) {
     
        try {
            String filePath = filePathAndName;
            filePath = filePath.toString();
            File myFilePath = new File(filePath);
            if (!myFilePath.exists()) {
                myFilePath.createNewFile();
            }
            FileWriter resultFile = new FileWriter(myFilePath);
            PrintWriter myFile = new PrintWriter(resultFile);
            String strContent = fileContent;
            myFile.println(strContent);
            myFile.close();
            resultFile.close();
        }
        catch (Exception e) {
            message = "创建文件操作出错";
        }
    }


    /**
     * 有编码方式的文件创建
     * @param filePathAndName 文本文件完整绝对路径及文件名
     * @param fileContent 文本文件内容
     * @param encoding 编码方式 例如 GBK 或者 UTF-8
     * @return
     */
    public static void createFile(String filePathAndName, String fileContent, String encoding) {
     
        try {
            String filePath = filePathAndName;
            filePath = filePath.toString();
            File myFilePath = new File(filePath);
            if (!myFilePath.exists()) {
                myFilePath.createNewFile();
            }
            PrintWriter myFile = new PrintWriter(myFilePath,encoding);
            String strContent = fileContent;
            myFile.println(strContent);
            myFile.close();
        }
        catch (Exception e) {
            message = "创建文件操作出错";
        }
    }


    /**
     * 删除文件
     * @param filePathAndName 文本文件完整绝对路径及文件名
     * @return Boolean 成功删除返回true遭遇异常返回false
     */
    public static boolean delFile(String filePathAndName) {
     boolean bea = false;
        try {
            String filePath = filePathAndName;
            File myDelFile = new File(filePath);
            if(myDelFile.exists()){
             myDelFile.delete();
             bea = true;
            }else{
             bea = false;
             message = (filePathAndName+"删除文件操作出错");
            }
        }
        catch (Exception e) {
            message = e.toString();
        }
        return bea;
    }
   


    /**
     * 删除文件夹
     * @param folderPath 文件夹完整绝对路径
     * @return
     */
    public static void delFolder(String folderPath) {
        try {
            delAllFile(folderPath); //删除完里面所有内容
            String filePath = folderPath;
            filePath = filePath.toString();
            java.io.File myFilePath = new java.io.File(filePath);
            myFilePath.delete(); //删除空文件夹
        }
        catch (Exception e) {
            message = ("删除文件夹操作出错");
        }
    }
    
    
    /**
     * 删除指定文件夹下所有文件
     * @param path 文件夹完整绝对路径
     * @return
     * @return
     */
    public static boolean delAllFile(String path) {
     boolean bea = false;
        File file = new File(path);
        if (!file.exists()) {
            return bea;
        }
        if (!file.isDirectory()) {
            return bea;
        }
        String[] tempList = file.list();
        File temp = null;
        for (int i = 0; i < tempList.length; i++) {
            if (path.endsWith(File.separator)) {
                temp = new File(path + tempList[i]);
            }else{
                temp = new File(path + File.separator + tempList[i]);
            }
            if (temp.isFile()) {
                temp.delete();
            }
            if (temp.isDirectory()) {
                delAllFile(path+"/"+ tempList[i]);//先删除文件夹里面的文件
                delFolder(path+"/"+ tempList[i]);//再删除空文件夹
                bea = true;
            }
        }
        return bea;
    }


    /**
     * 复制单个文件
     * @param oldPathFile 准备复制的文件源
     * @param newPathFile 拷贝到新绝对路径带文件名
     * @return
     */
    public static void copyFile(String oldPathFile, String newPathFile) {
        try {
            int bytesum = 0;
            int byteread = 0;
            File oldfile = new File(oldPathFile);
            if (oldfile.exists()) { //文件存在时
                InputStream inStream = new FileInputStream(oldPathFile); //读入原文件
                FileOutputStream fs = new FileOutputStream(newPathFile);
                byte[] buffer = new byte[1444];
                while((byteread = inStream.read(buffer)) != -1){
                    bytesum += byteread; //字节数 文件大小
                    System.out.println(bytesum);
                    fs.write(buffer, 0, byteread);
                }
                inStream.close();
            }
        }catch (Exception e) {
            message = ("复制单个文件操作出错");
        }
    }
   

    /**
     * 复制整个文件夹的内容
     * @param oldPath 准备拷贝的目录
     * @param newPath 指定绝对路径的新目录
     * @return
     */
    public static void copyFolder(String oldPath, String newPath) {
        try {
            new File(newPath).mkdirs(); //如果文件夹不存在 则建立新文件夹
            File a=new File(oldPath);
            String[] file=a.list();
            File temp=null;
            for (int i = 0; i < file.length; i++) {
                if(oldPath.endsWith(File.separator)){
                    temp=new File(oldPath+file[i]);
                }else{
                    temp=new File(oldPath+File.separator+file[i]);
                }
                if(temp.isFile()){
                    FileInputStream input = new FileInputStream(temp);
                    FileOutputStream output = new FileOutputStream(newPath + "/" +
                    (temp.getName()).toString());
                    byte[] b = new byte[1024 * 5];
                    int len;
                    while ((len = input.read(b)) != -1) {
                        output.write(b, 0, len);
                    }
                    output.flush();
                    output.close();
                    input.close();
                }
                if(temp.isDirectory()){//如果是子文件夹
                    copyFolder(oldPath+"/"+file[i],newPath+"/"+file[i]);
                }
            }
        }catch (Exception e) {
            message = "复制整个文件夹内容操作出错";
        }
    }


    /**
     * 移动文件
     * @param oldPath
     * @param newPath
     * @return
     */
    public static void moveFile(String oldPath, String newPath) {
        copyFile(oldPath, newPath);
        delFile(oldPath);
    }
   

    /**
     * 移动目录
     * @param oldPath
     * @param newPath
     * @return
     */
    public static void moveFolder(String oldPath, String newPath) {
        copyFolder(oldPath, newPath);
        delFolder(oldPath);
    }
    /**
     * 得到错误信息
     */
    public static String getMessage(){
        return message;
    }
}
```
# StringUtils

1. 字符串达到多长才截取

2. 将指定的对象转换为String类型

3. 转换字符,用于替换提交的数据中存在非法数据:"'"

4. 对标题""转换为中文“”采用对应转换

5. 替换HTML标记

6. 标题中含有特殊字符替换 如:●▲@◎※ 主要在标题中使用
7. 替换所有英文字母

8. 替换所有数字

9. 将/n转换成为回车<br> ,空格转为&nbsp;

10. 清除所有<>标记符号 主要在搜索中显示文字内容 而不显示样式

11. 清楚WOrd垃圾代码

12. 判断传入的字符串如果为null则返回"",否则返回其本身

13. 获取百分比


```java
package com.xwtech.uomp.base.util;

import java.io.UnsupportedEncodingException;
import java.text.NumberFormat;
import java.util.HashMap;
import java.util.Map;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import com.xwtech.uomp.base.action.handler.HandlerResult;
import com.xwtech.uomp.base.constants.SystemCodeConstants;

public class StringUtil {

    public static final String arrTest[] = {"[br]", "[/b]", "[/i]", "[/u]", "[/size]", "[/color]", "[/align]", "[/url]", "[/email]", "[/img]"};
    public static final String arrParam[] = {"\\[br\\]", "\\[b\\](.+?)\\[/b\\]",
            "\\[i\\](.+?)\\[/i\\]",
            "\\[u\\](.+?)\\[/u\\]",
            "\\[size=(.+?)\\](.+?)\\[/size\\]",
            "\\[color=(.+?)\\](.+?)\\[/color\\]",
            "\\[align=(.+?)\\](.+?)\\[/align\\]",
            "\\[url=(.+?)\\](.+?)\\[/url\\]",
            "\\[email=(.+?)\\](.+?)\\[/email\\]," +
                    "\\[img=(.+?)\\](.+?)\\[/img\\]"};
    public static final String arrCode[] = {"<br>", "<b>$1</b>", "<i>$1</i>", "<u>$1</u>",
            "<font size=\"$1\">$2</font>",
            "<font color=\"$1\">$2</font>",
            "<div align=\"$1\">$2</div>",
            "<a href=\"$1\" target=\"_blank\">$2</a>",
            "<a href=\"email:$1\">$2</a>",
            "<img src=\"$1\" border=0>$2</img>"};


    public static int getInt(String content) {
        int intContent;
        try {
            intContent = Integer.parseInt(content);
        } catch (Exception e) {
            intContent = 0;
        }
        return intContent;
    }

    public static long getLong(String content) {
        long lngContent;
        try {
            lngContent = Long.parseLong(content);
        } catch (Exception e) {
            lngContent = 0L;
        }
        return lngContent;
    }

    /** 1
     * @param str    原字符串
     * @param length 字符串达到多长才截取
     * @return
     */
    @SuppressWarnings("static-access")
	public static String subStringToPoint(String str, int length, String more) {

        String reStr = "";

        if (str.length() * 2 - 1 > length) {

            int reInt = 0;

            if (str == null)

                return "";

            char[] tempChar = str.toCharArray();

            for (int kk = 0; (kk < tempChar.length && length > reInt); kk++) {

                String s1 = str.valueOf(tempChar[kk]);

                byte[] b = s1.getBytes();

                reInt += b.length;

                reStr += tempChar[kk];

            }

            if (length == reInt || (length == reInt - 1)) {

                if (!reStr.equals(str)) {
                    reStr += more;
                }
            }

        } else {
            reStr = str;
        }
        return reStr;

    }


    /** 2
     * 将指定的对象转换为String类型
     *
     * @param curObject 传入对象参数
     * @return String
     */
    public static String getString(Object curObject) {
        if (null == curObject) {
            throw new NullPointerException("The input object is null.");
        } else {
            return curObject.toString();
        }
    }

    /** 3
     * 转换字符,用于替换提交的数据中存在非法数据:"'"
     *
     * @param Content
     * @return
     */
    public static String replaceChar(String content) {
        String newstr = "";
        newstr = content.replaceAll("\'", "''");
        return newstr;
    }

    /**4
     * 对标题""转换为中文“”采用对应转换
     *
     * @param Content
     * @return
     */
    public static String replaceSymbol(String content) {
        int intPlaceNum = 0;
        int Num = 0;
        String strContent = content;
        while (true) {
            //判断是否还存在"
            intPlaceNum = strContent.indexOf("\"");
            if (intPlaceNum < 0) {
                break;
            } else {
                if (Num % 2 == 0) {
                    strContent = strContent.replaceFirst("\"", "“");
                } else {
                    strContent = strContent.replaceFirst("\"", "”");
                }
                Num = Num + 1;
            }
        }
        return strContent;
    }

    /**5
     * 替换HTML标记
     *
     * @param Content
     * @return
     */
    public static String replaceCharToHtml(String content) {
        String strContent = content;
        strContent = strContent.replaceAll("<", "<");
        strContent = strContent.replaceAll(">", ">");
        strContent = strContent.replaceAll("\"", """);
        return strContent;
    }

    public static String replaceHtmlToChar(String content) {
        String strContent = content;
        strContent = strContent.replaceAll("<", "<");
        strContent = strContent.replaceAll(">", ">");
        strContent = strContent.replaceAll(""", "\"");
        return strContent;
    }

    //数据库替换
    public static String replaceCharToSql(String content) {
        String strContent = content;
        strContent = strContent.replaceAll("%", "\\\\%");
        return strContent;
    }

    public static String toHtmlValue(String value) {
        if (null == value) {
            return null;
        }
        char a = 0;
        StringBuffer buf = new StringBuffer();
        for (int i = 0; i < value.length(); i++) {
            a = value.charAt(i);
            switch (a) {
                // 双引号
                case 34:
                    buf.append(""");
                    break;
                // &号
                case 38:
                    buf.append("&");
                    break;
                // 单引号
                case 39:
                    buf.append("'");
                    break;
                // 小于号
                case 60:
                    buf.append("<");
                    break;
                // 大于号
                case 62:
                    buf.append(">");
                    break;
                default:
                    buf.append(a);
                    break;
            }
        }
        return buf.toString();
    }


    /**6
     * 标题中含有特殊字符替换 如:●▲@◎※ 主要在标题中使用
     *
     * @param Content
     * @return
     */
    public static String replaceSign(String content) {
        String strContent = "";
        strContent = content.replaceAll("\\*", "");
        strContent = strContent.replaceAll("\\$", "");
        strContent = strContent.replaceAll("\\+", "");
        String arrStr[] = {":", "：", "●", "▲", "■", "@", "＠",
                "◎", "★", "※", "＃", "〓", "＼", "§", "☆",
                "○", "◇", "◆", "□", "△", "＆", "＾", "￣",
                "＿", "♂", "♀", "Ю", "┭", "①", "「", "」", "≮", "§",
                "￡", "∑", "『", "』", "⊙", "∷", "Θ", "の", "↓", "↑",
                "Ф", "~", "Ⅱ", "∈", "┣", "┫", "╋", "┇", "┋", "→",
                "←", "!", "Ж", "#"};
        for (int i = 0; i < arrStr.length; i++) {
            if ((strContent.indexOf(arrStr[i])) >= 0) {
                strContent = strContent.replaceAll(arrStr[i], "");
            }
        }

        return strContent;
    }

    /**7
     * 替换所有英文字母
     *
     * @param Content
     * @return
     */
    public static String replaceLetter(String content) {
        String strMark = "[^[A-Za-z]+$]";
        String strContent = "";
        strContent = content.replaceAll(strMark, "");
        return strContent;
    }

    /**8
     * 替换所有数字
     *
     * @param Content
     * @return
     */
    public static String replaceNumber(String content) {
        String strMark = "[^[0-9]+$]";
        String strContent = "";
        strContent = content.replaceAll(strMark, "");
        return strContent;
    }

    /**9
     * 将/n转换成为回车<br> ,空格转为 
     *
     * @param Content
     * @return
     */
    public static String replaceBr(String content) {
        if (content == null) {
            return "";
        }
        String strContent = "";

        // String strMark ="[/\n\r\t]";

        //strContent = content.replaceAll(strMark,"<br>");

        strContent = content.replaceAll("\n\r\t", "<br>");
        strContent = strContent.replaceAll("\n\r", "<br>");
        strContent = strContent.replaceAll("\r\n", "<br>");
        strContent = strContent.replaceAll("\n", "<br>");
        strContent = strContent.replaceAll("\r", "<br>");
        strContent = strContent.replaceAll(" ", " ");
        return strContent;
    }

    /**10
     * 清除所有<>标记符号 主要在搜索中显示文字内容 而不显示样式
     *
     * @param Content
     * @return
     */
    public static String replaceMark(String content) {
        String strContent = "";
        String strMark = "<\\s*[^>]*>";
        strContent = content.trim();
        strContent = strContent.replaceAll("\"", "");
        strContent = strContent.replaceAll("\'", "");
        //删除所有<>标记
        strContent = strContent.replaceAll(strMark, "");
        strContent = strContent.replaceAll(" ", "");
        strContent = strContent.replaceAll(" ", "");
        strContent = strContent.replaceAll("　", "");
        strContent = strContent.replaceAll("\r", "");
        strContent = strContent.replaceAll("\n", "");
        strContent = strContent.replaceAll("\r\n", "");
        return strContent;
    }

    /**11
     * 清楚WOrd垃圾代码
     *
     * @param Content
     * @return
     */
    public static String clearWord(String content) {
        String strContent = "";
        strContent = content.trim();
        strContent = strContent.replaceAll("x:str", "");
        //Remove Style attributes
        strContent = strContent.replaceAll("<(\\w[^>]*) style=\"([^\"]*)\"", "<$1");
        //Remove all SPAN  tags
        strContent = strContent.replaceAll("<\\/?SPAN[^>]*>", "");
        //Remove Lang attributes
        strContent = strContent.replaceAll("<(\\w[^>]*) lang=([^ |>]*)([^>]*)", "<$1$3");
        //Remove Class attributes
        strContent = strContent.replaceAll("<(\\w[^>]*) class=([^ |>]*)([^>]*)", "<$1$3");
        //Remove XML elements and declarations
        strContent = strContent.replaceAll("<\\\\?\\?xml[^>]*>", "");
        //Remove Tags with XML namespace declarations: <o:p></o:p>
        strContent = strContent.replaceAll("<\\/?\\w+:[^>]*>", "");
        return strContent;
    }

    /**
     * 对组ID信息进行处理 转换为标准ID组 并过滤重复的信息
     *
     * @param teamId
     * @return
     */
    public static String checkTeamId(String teamId) {
        String strTeamId = "";
        String strTempId = "";
        String strTemp = "";
        String[] arrTeamId = teamId.split(",");
        for (int num = 0; num < arrTeamId.length; num++) {
            strTemp = arrTeamId[num].trim();
            if ((!strTemp.equals("")) && (!strTemp.equals("0"))) {
                if ((strTempId.indexOf("," + strTemp + ",")) >= 0) { //表示已经保存过了
                } else {
                    if (strTeamId.equals("")) {
                        strTeamId = strTemp;
                        strTempId = strTempId + "," + strTemp + ",";
                        ;
                    } else {
                        strTeamId = strTeamId + "," + strTemp;
                        strTempId = strTempId + strTemp + ",";
                    }
                }
            }
        }
        return strTeamId;
    }


    public static String replaceUbb(String content) {
        String strContent = content;
        try {
            for (int num = 0; num < arrTest.length; num++) {
                if ((strContent.indexOf(arrTest[num])) >= 0) {
                    try {
                        strContent = strContent.replaceAll(arrParam[num], arrCode[num]);
                    } catch (Exception ex) {
                    }
                }
            }
        } catch (Exception e) {
            //System.out.println("UBB CODE 错误"+e);
        }
        return strContent;
    }


    /**12
     * 判断传入的字符串如果为null则返回"",否则返回其本身
     *
     * @param string
     * @param instant
     * @return String
     */
    public static String convertNull(String string, String instant) {
        return isNull(string) ? instant : string;
    }

    /**
     * {@link #convertNull(String, String)}
     *
     * @param string
     * @return String
     */
    public static String convertNull(String string) {
        return convertNull(string, "");
    }

    /**
     * 判断对象是否为空
     *
     * @param obj Object
     * @return boolean 空返回true,非空返回false
     */
    public static boolean isNull(Object obj) {
        return (null == obj) ? true : false;
    }

    /**
     * Description:判断字段空null <br>
     *
     * @param s
     * @return boolean
     */
    public static boolean isNull(String s) {
        if (s == null || "".equals(s.trim())) {
            return true;
        }

        return false;
    }

    /**13
     * 获取百分比
     *
     * @param p1
     * @param p2
     * @return
     */
    public static String percent(double p1, double p2) {
        if (p2 == 0) {
            return "0.00%";
        }
        String str;
        double p3 = p1 / p2;
        NumberFormat nf = NumberFormat.getPercentInstance();
        nf.setMinimumFractionDigits(2);
        str = nf.format(p3);
        return str;
    }

    /**
     * 字符串编码转换的实现方法
     *
     * @param str        待转换编码的字符串
     * @param oldCharset 原编码
     * @param newCharset 目标编码
     * @return
     * @throws UnsupportedEncodingException
     */
    public static String changeCharset(String str, String oldCharset, String newCharset) {
        try {
            if (str != null) {
                //用旧的字符编码解码字符串。解码可能会出现异常。
                byte[] bs = str.getBytes(oldCharset);
                //用新的字符编码生成字符串
                return new String(bs, newCharset);
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return "";
        }
        return "";
    }

    /**
     * 字符串编码转换的实现方法
     *
     * @param str        待转换编码的字符串
     * @param newCharset 目标编码
     * @return
     * @throws UnsupportedEncodingException
     */
    public String changeCharset(String str, String newCharset) {
        try {
            if (str != null) {
                //用默认字符编码解码字符串。
                byte[] bs = str.getBytes();
                //用新的字符编码生成字符串
                return new String(bs, newCharset);
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return "";
    }

    /**
     * 解析html中的参数信息
     *
     * @param elementStr
     * @return
     */
    public static Map<String, String> getConfigValue(String elementStr) {
        try {
            elementStr = java.net.URLDecoder.decode(elementStr, "utf-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        int start = elementStr.indexOf("configvalue");
        Map<String, String> map = null; //参数的键值对
        if (start != -1) {
            map = new HashMap<String, String>();
            start = elementStr.indexOf("\"", start);
            int end = elementStr.lastIndexOf("||");
            if (start < 0 || end < 0) {
                return null;
            }
            String configValue = elementStr.substring(start + 1, end);
            String[] values = configValue.split("\\|\\|");

            for (int i = 0; i < values.length; i++) {
                String value = values[i];
                if (value != null && value.trim().length() > 1) {
                    int de = value.indexOf("=");
                    if (de > 0) {
                        String name = value.substring(0, de);
                        String v = value.substring(de + 1);
                        map.put(name, v);
                    }
                }
            }
        }
        return map;
    }

    /**
     * 转换空值为0
     *
     * @param str
     * @return
     */
    public static String conventString(String str) {
        return null == str || "".equals(str) ? "" + "0" : str;
    }

    public static String alert(HandlerResult result, String contextPath) {
        StringBuffer sf = new StringBuffer();
        sf.append("<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\">");
        sf.append("<html><head><title>信息提示</title>");
        sf.append("<link rel=\"stylesheet\" type=\"text/css\" href=\"").append(contextPath).append("/resource/css/frame.css\"  />");
        sf.append("<link rel=\"stylesheet\" type=\"text/css\" href=\"").append(contextPath).append("/resource/scripts/Dhtmlx/dhtmlxSuite/dhtmlxWindows/codebase/skins/dhtmlxwindows_dhx_skyblue.css\"/>");
        sf.append("<link rel=\"stylesheet\" type=\"text/css\" href=\"").append(contextPath).append("/resource/scripts/Dhtmlx/dhtmlxSuite/dhtmlxWindows/codebase/dhtmlxwindows.css\"/>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/Dhtmlx/dhtmlxSuite/dhtmlxLayout/codebase/dhtmlxcommon.js\"></script>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/Dhtmlx/dhtmlxSuite/dhtmlxLayout/codebase/dhtmlxcontainer.js\"></script>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/Dhtmlx/dhtmlxSuite/dhtmlxWindows/codebase/dhtmlxwindows.js\"></script>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/jquery-1.7.1.min.js\"></script>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/uompDialog.js\"></script>");
        sf.append("<script type=\"text/javascript\" language=\"javascript\" src=\"").append(contextPath).append("/resource/scripts/main.js\" ></script>");
        sf.append("</head><body>");
        sf.append("</body></html>");
        sf.append("<script type=\"text/javascript\">");
        sf.append("$(document).ready(function(){");
        sf.append(" top.UOMPDialog.alert('" + result.getResMsg() + "',0,\"\"");
        if (SystemCodeConstants.NOT_LOGIN.equals(result.getSysCode())) {
            sf.append(", {'yes' : function(){");
            sf.append(" top.location.href = '").append(contextPath).append("/index.jsp'").append(";");
            sf.append("}}");
        }
        sf.append(");");
        sf.append(" });");
        sf.append("</script>");
        return sf.toString();
    }

    public static void main(String[] args) {
        Pattern pattern = Pattern.compile("<span\\s.+?]]</span>");
        String str = "<span configvalue=\"eid=1043||ename=%E9%9D%A2%E5%8C%85%E5%B1%91||folderId=CLBLYM||\" contenteditable=\"false\" style=\"background-color: #ffff00; color: #000000\">[[ 面包屑 ]]</span><br />"
                + "业务名称：<span configvalue=\"eid=1042||ename=%E4%B8%9A%E5%8A%A1%E5%90%8D%E7%A7%B0||busiNum=CL||\" contenteditable=\"false\" style=\"background-color: #ffff00; color: #000000\">[[ 业务名称 ]]</span><br />"
                + "业务资费：<span configvalue=\"eid=1041||ename=%E4%B8%9A%E5%8A%A1%E8%B5%84%E8%B4%B9||busiNum=CL||\" contenteditable=\"false\" style=\"background-color: #ffff00; color: #000000\">[[ 业务资费 ]]</span><br />"
                + "业务介绍：<br />"
                + "<span configvalue=\"eid=1040||ename=%E4%B8%9A%E5%8A%A1%E4%BB%8B%E7%BB%8D||busiNum=CL||\" contenteditable=\"false\" style=\"background-color: #ffff00; color: #000000\">[[ 业务介绍 ]]</span><br />"
                + "<br />"
                + "<br />"
                + "<br /><span >]]</span>"
                + "<span configvalue=\"eid=1043||ename=%E9%9D%A2%E5%8C%85%E5%B1%91||folderId=CLBLYM||\" contenteditable=\"false\" style=\"background-color: #ffff00; color: #000000\">[[ 面包屑 ]]</span><br />";
        Matcher matcher = pattern.matcher(str);
        String htmlStr = "";
        StringBuffer strbuff = new StringBuffer();

        int i = 0;
        while (matcher.find()) {
            String bm = matcher.group();
            System.out.println(bm);
            Map map = getConfigValue(bm);
            if (map != null) {
                //todo:从缓存中获取数据
                if (((String) map.get("eid")).equals("1043")) {
                    String se = "【掌上营业厅】+ 1";
                    matcher.appendReplacement(strbuff, Matcher.quoteReplacement(se == null ? "" : se));
                } else if (((String) map.get("eid")).equals("1042")) {
                    String se = "【掌上营业厅】+ 2";
                    matcher.appendReplacement(strbuff, Matcher.quoteReplacement(se == null ? "" : se));
                } else if (((String) map.get("eid")).equals("1041")) {
                    String se = "【掌上营业厅】 + 3";
                    matcher.appendReplacement(strbuff, Matcher.quoteReplacement(se == null ? "" : se));
                } else if (((String) map.get("eid")).equals("1040")) {
                    String se = "【掌上营业厅】+ 4";
                    matcher.appendReplacement(strbuff, Matcher.quoteReplacement(se == null ? "" : se));
                } else if (((String) map.get("eid")).equals("1046")) {
                    String se = "【掌上营业厅】+888888888888";
                    matcher.appendReplacement(strbuff, Matcher.quoteReplacement(se == null ? "" : se));
                }
                i++;
            }
        }
        matcher.appendTail(strbuff);
        htmlStr += strbuff.toString();
        System.out.println(htmlStr + "=================" + i);
    }
    
    /**
     * 方法描述：判断传入的字符串是否非空，即：字符串是否等于null、""或" "。
     * 创建日期：2013-12-7下午11:39:34
     * 修改日期：
     * 作者：zhanglu
     * @param:
     * @return:boolean
     */
    public static boolean isNotEmpty(String str)
	{
		if ((null == str) || ("".equals(str.trim())))
		{
			return false;
		}
		else
		{
			return true;
		}
	}
    
    /**
     * 方法描述：将传入的字符串转换成整型数据，如果转换过程中发生异常，则返回默认值：defaultValue。
     * 创建日期：2013-12-7下午11:40:20
     * 修改日期：
     * 作者：zhanglu
     * @param:
     * @return:int
     */
    public static int convertIntoInt(String str, int defaultValue)
	{
		// 定义一个返回值，假如转型过程中发生异常，则返回此默认值
		int retData = defaultValue;
		try
		{
			retData = Integer.parseInt(str.trim());
		}
		catch(NumberFormatException e)
		{
		}
		catch(Exception ex)
		{
		}

		return retData;
	}

}

```
org.apache.commons.lang.StringUtils中方法的操作对象是java.lang.String类型的对象，是JDK提供的String类型操作方法的补充，并且是null安全的(即如果输入参数String为null则不会抛出NullPointerException，而是做了相应处理，例如，如果输入为null则返回也是null等，具体可以查看源代码)。

除了构造器，StringUtils中一共有130多个方法，并且都是static的，

所以我们可以这样调用StringUtils.xxx()。

下面分别对一些常用方法做简要介绍：

1. public static boolean isEmpty(String str)

### 判断某字符串是否为空，为空的标准是str == null 或 str.length() == 0

下面是示例：


```java
StringUtils.isEmpty(null)          = true

StringUtils.isEmpty("")       = true

StringUtils.isEmpty(" ")      = false

StringUtils.isEmpty("        ")     = false

StringUtils.isEmpty("bob")       = false

StringUtils.isEmpty(" bob ") = false
```


2. public static boolean isNotEmpty(String str)

### 判断某字符串是否非空，等于!isEmpty(String str)

下面是示例：


```java
StringUtils.isNotEmpty(null)        = false

StringUtils.isNotEmpty("")           = false

StringUtils.isNotEmpty(" ")      = true

StringUtils.isNotEmpty("         ")    = true

StringUtils.isNotEmpty("bob")   = true

StringUtils.isNotEmpty(" bob ")   = true
```


3. public static boolean isBlank(String str)

### 判断某字符串是否为空或长度为0或由空白符(whitespace)构成

下面是示例：


```java
StringUtils.isBlank(null)         = true

StringUtils.isBlank("")            = true

StringUtils.isBlank(" ")                 = true

StringUtils.isBlank("        ")          = true

StringUtils.isBlank("\t \n \f \r")    = true

StringUtils.isBlank("\b")               = false

StringUtils.isBlank("bob")            = false

StringUtils.isBlank(" bob ")        = false
```


4. public static boolean isNotBlank(String str)

### 判断某字符串是否不为空且长度不为0且不由空白符(whitespace)构成，

等于!isBlank(String str)

下面是示例：


```java
StringUtils.isNotBlank(null)        = false

StringUtils.isNotBlank("")               = false

StringUtils.isNotBlank(" ")          = false

StringUtils.isNotBlank("         ")        = false

StringUtils.isNotBlank("\t \n \f \r")     = false

StringUtils.isNotBlank("\b")              = true

StringUtils.isNotBlank("bob")           = true

StringUtils.isNotBlank(" bob ")   = true
```


5. public static String trim(String str)

### 去掉字符串两端的控制符(control characters, char <= 32)

如果输入为null则返回null

下面是示例：


```java
StringUtils.trim(null)                = null

StringUtils.trim("")                 = ""

StringUtils.trim(" ")                          = ""

StringUtils.trim("     \b \t \n \f \r    ") = ""

StringUtils.trim("     \n\tss   \b")        = "ss"

StringUtils.trim(" d   d dd     ")     = "d   d dd"

StringUtils.trim("dd     ")            = "dd"

StringUtils.trim("     dd       ")           = "dd"
```


6. public static String trimToNull(String str)

### 去掉字符串两端的控制符(control characters, char <= 32)

如果变为null或""，则返回null

下面是示例：


```java
StringUtils.trimToNull(null)                = null

StringUtils.trimToNull("")                           = null

StringUtils.trimToNull(" ")                          = null

StringUtils.trimToNull("     \b \t \n \f \r    ") = null

StringUtils.trimToNull("     \n\tss   \b")     = "ss"

StringUtils.trimToNull(" d   d dd     ")        = "d   d dd"

StringUtils.trimToNull("dd     ")           = "dd"

StringUtils.trimToNull("     dd       ")          = "dd"
```

7. public static String trimToEmpty(String str)

### 去掉字符串两端的控制符(control characters, char <= 32)

如果变为null或""，则返回""

下面是示例：


```java
StringUtils.trimToEmpty(null)                    = ""

StringUtils.trimToEmpty("")                   = ""

StringUtils.trimToEmpty(" ")                          = ""

StringUtils.trimToEmpty("     \b \t \n \f \r    ") = ""

StringUtils.trimToEmpty("     \n\tss   \b")     = "ss"

StringUtils.trimToEmpty(" d   d dd     ")      = "d   d dd"

StringUtils.trimToEmpty("dd     ")             = "dd"

StringUtils.trimToEmpty("     dd       ")          = "dd"
```

8. public static String strip(String str)

### 去掉字符串两端的空白符(whitespace)，

如果输入为null则返回null

下面是示例(注意和trim()的区别)：

```java
StringUtils.strip(null)                    = null

StringUtils.strip("")                   = ""

StringUtils.strip(" ")                  = ""

StringUtils.strip("     \b \t \n \f \r    ")        = "\b"

StringUtils.strip("     \n\tss   \b")             = "ss   \b"

StringUtils.strip(" d   d dd     ")      = "d   d dd"

StringUtils.strip("dd     ")             = "dd"

StringUtils.strip("     dd       ")          = "dd"
```

9. public static String stripToNull(String str)

### 去掉字符串两端的空白符(whitespace)，

如果变为null或""，则返回null

下面是示例(注意和trimToNull()的区别)：


```java
StringUtils.stripToNull(null)                   = null

StringUtils.stripToNull("")                    = null

StringUtils.stripToNull(" ")                 = null

StringUtils.stripToNull("     \b \t \n \f \r    ") = "\b"

StringUtils.stripToNull("     \n\tss   \b")       = "ss   \b"

StringUtils.stripToNull(" d   d dd     ")       = "d   d dd"

StringUtils.stripToNull("dd     ")                 = "dd"

StringUtils.stripToNull("     dd       ")          = "dd"
```


10. public static String stripToEmpty(String str)

### 去掉字符串两端的空白符(whitespace)，

如果变为null或""，则返回""

下面是示例(注意和trimToEmpty()的区别)：


```java
StringUtils.stripToNull(null)                  = ""

StringUtils.stripToNull("")                   = ""

StringUtils.stripToNull(" ")                  = ""

StringUtils.stripToNull("     \b \t \n \f \r    ")   = "\b"

StringUtils.stripToNull("     \n\tss   \b")     = "ss   \b"

StringUtils.stripToNull(" d   d dd     ")     = "d   d dd"

StringUtils.stripToNull("dd     ")                 = "dd"

StringUtils.stripToNull("     dd       ")           = "dd"
```

 

11. public static String strip(String str, String stripChars)

### 去掉str两端的在stripChars中的字符。

如果str为null或等于""，则返回它本身；

如果stripChars为null或""，则返回strip(String str)。

12. public static String stripStart(String str, String stripChars)

### 和11相似，去掉str前端的在stripChars中的字符。

13. public static String stripEnd(String str, String stripChars)

### 和11相似，去掉str末端的在stripChars中的字符。

14. public static String[] stripAll(String[] strs)

### 对字符串数组中的每个字符串进行strip(String str)，然后返回。

如果strs为null或strs长度为0，则返回strs本身

15. public static String[] stripAll(String[] strs, String stripChars)

### 对字符串数组中的每个字符串进行strip(String str, String stripChars)，然后返回。

如果strs为null或strs长度为0，则返回strs本身

16. public static boolean equals(String str1, String str2)

### 比较两个字符串是否相等，如果两个均为空则也认为相等。

17. public static boolean equalsIgnoreCase(String str1, String str2)

### 比较两个字符串是否相等，不区分大小写，如果两个均为空则也认为相等。

18. public static int indexOf(String str, char searchChar)

### 返回字符searchChar在字符串str中第一次出现的位置。

如果searchChar没有在str中出现则返回-1，

如果str为null或""，则也返回-1

19. public static int indexOf(String str, char searchChar, int startPos)

### 返回字符searchChar从startPos开始在字符串str中第一次出现的位置。

如果从startPos开始searchChar没有在str中出现则返回-1，

如果str为null或""，则也返回-1

20. public static int indexOf(String str, String searchStr)

### 返回字符串searchStr在字符串str中第一次出现的位置。

如果str为null或searchStr为null则返回-1，

如果searchStr为"",且str为不为null，则返回0，

如果searchStr不在str中，则返回-1

21. public static int ordinalIndexOf(String str, String searchStr, int ordinal)

### 返回字符串searchStr在字符串str中第ordinal次出现的位置。

如果str=null或searchStr=null或ordinal<=0则返回-1

举例(*代表任意字符串)：


```java
StringUtils.ordinalIndexOf(null, *, *)          = -1

StringUtils.ordinalIndexOf(*, null, *)          = -1

StringUtils.ordinalIndexOf("", "", *)           = 0

StringUtils.ordinalIndexOf("aabaabaa", "a", 1) = 0

StringUtils.ordinalIndexOf("aabaabaa", "a", 2) = 1

StringUtils.ordinalIndexOf("aabaabaa", "b", 1) = 2

StringUtils.ordinalIndexOf("aabaabaa", "b", 2) = 5

StringUtils.ordinalIndexOf("aabaabaa", "ab", 1) = 1

StringUtils.ordinalIndexOf("aabaabaa", "ab", 2) = 4

StringUtils.ordinalIndexOf("aabaabaa", "bc", 1) = -1

StringUtils.ordinalIndexOf("aabaabaa", "", 1)   = 0

StringUtils.ordinalIndexOf("aabaabaa", "", 2)   = 0
```


22. public static int indexOf(String str, String searchStr, int startPos)

### 返回字符串searchStr从startPos开始在字符串str中第一次出现的位置。

举例(*代表任意字符串)：


```java
StringUtils.indexOf(null, *, *)          = -1

StringUtils.indexOf(*, null, *)          = -1

StringUtils.indexOf("", "", 0)           = 0

StringUtils.indexOf("aabaabaa", "a", 0) = 0

StringUtils.indexOf("aabaabaa", "b", 0) = 2

StringUtils.indexOf("aabaabaa", "ab", 0) = 1

StringUtils.indexOf("aabaabaa", "b", 3) = 5

StringUtils.indexOf("aabaabaa", "b", 9) = -1

StringUtils.indexOf("aabaabaa", "b", -1) = 2

StringUtils.indexOf("aabaabaa", "", 2)   = 2

StringUtils.indexOf("abc", "", 9)        = 3
```


23. public static int lastIndexOf(String str, char searchChar)

### 基本原理同18。

24. public static int lastIndexOf(String str, char searchChar, int startPos)

### 基本原理同19。

25. public static int lastIndexOf(String str, String searchStr)

### 基本原理同20。

26. public static int lastIndexOf(String str, String searchStr, int startPos)

### 基本原理同22。

27. public static boolean contains(String str, char searchChar)

### 判断字符串str中是否包含字符searchChar。

如果str为null或""，返回false；

如果searchChar不在str中，返回false。

28. public static boolean contains(String str, String searchStr)

### 判断字符串str是否包含字符串searchStr。

如果str为null或searchStr为null，返回false；

如果str为""，并且searchStr为""，返回true

举例：


```java
StringUtils.contains("", "")       = true

StringUtils.contains("dfg", "")    = true

StringUtils.contains("dfg", "d")   = true

StringUtils.contains("dfg", "gz") = false
```


29. public static boolean containsIgnoreCase(String str, String searchStr)

### 判断字符串str是否包含字符串searchStr，不区分大小写。

和28类似。

30. public static int indexOfAny(String str, char[] searchChars)

### 找出字符数组searchChars中的字符第一次出现在字符串str中的位置。

如果字符数组中的字符都不在字符串中，则返回-1

如果字符串为null或""，则返回-1

举例(*表示任意)：


```java
StringUtils.indexOfAny(null, *)                 = -1

StringUtils.indexOfAny("", *)                   = -1

StringUtils.indexOfAny(*, [])                   = -1

StringUtils.indexOfAny("asdf", ['a','f',' '])   = 0

StringUtils.indexOfAny("bs df", ['a','f',' ']) = 2

StringUtils.indexOfAny("bsdf", ['a','f',' '])   = 3

StringUtils.indexOfAny("bbeegg", ['a','f',' ']) = -1
```


31. public static int indexOfAny(String str, String searchChars)

### 找出字符串searchChars中的字符第一次出现在字符串str中的位置。

如果字符串searchChars中的字符都不在字符串str中，则返回-1

如果searchChars或str为null或为""，则返回-1

举例(*表示任意)：

```java
StringUtils.indexOfAny(null, *)         = -1

StringUtils.indexOfAny("", *)           = -1

StringUtils.indexOfAny(*, null)         = -1

StringUtils.indexOfAny(*, "")           = -1

StringUtils.indexOfAny("asdf", "af ")   = 0

StringUtils.indexOfAny("bs df", "af ") = 2

StringUtils.indexOfAny("bsdf", "af ")   = 3

StringUtils.indexOfAny("bbeegg", "af ") = -1
```


32. public static int indexOfAnyBut(String str, char[] searchChars)

### 找出字符串str中不在字符数组searchChars中的第一个字符的位置。

如果字符串中的所有字符都在字符数组中，则返回-1

如果字符串为null或""，则返回-1

举例(*表示任意)：


```java
StringUtils.indexOfAnyBut(null, *)                 = -1

StringUtils.indexOfAnyBut("", *)                   = -1

StringUtils.indexOfAnyBut(*, [])                   = -1

StringUtils.indexOfAnyBut("asdf", ['a','f',' '])   = 1

StringUtils.indexOfAnyBut("bs df", ['a','f',' ']) = 0

StringUtils.indexOfAnyBut(" aaf", ['a','f',' '])   = -1

StringUtils.indexOfAnyBut("bbeegg", ['a','f',' ']) = 0
```


33. public static int indexOfAnyBut(String str, String searchChars)

### 找出字符串str中不在字符串searchChars中的第一个字符的位置。

如果字符串str中的所有字符都在字符串searchChars中，则返回-1

如果字符串str或searchChars为null或""，则返回-1

举例(*表示任意)：


```java
StringUtils.indexOfAnyBut(null, *)         = -1

StringUtils.indexOfAnyBut("", *)           = -1

StringUtils.indexOfAnyBut(*, null)         = -1

StringUtils.indexOfAnyBut(*, "")           = -1

StringUtils.indexOfAnyBut("asdf", "af ")   = 1

StringUtils.indexOfAnyBut("bs df", "af ") = 0

StringUtils.indexOfAnyBut(" aaf", "af ")   = -1

StringUtils.indexOfAnyBut("bbeegg", "af ") = 0
```


34. public static boolean containsOnly(String str, char[] valid)

### 判断是否字符串str仅包含字符数组valid中的字符，即字符串中的字符是否都在字符数组中。

如果str为null，则返回false；如果str为""，则返回true

举例(*表示任意)：


```java
StringUtils.containsOnly(null, *))              = false

StringUtils.containsOnly("", *))                = true

StringUtils.containsOnly("afaf", ['a','f',' ']))= true

StringUtils.containsOnly("af a", ['a','f',' ']))= true

StringUtils.containsOnly("a", ['a','f',' ']))   = true

StringUtils.containsOnly("afg", ['a','f',' '])) = false

StringUtils.containsOnly("bbeegg", []))         = false
```


35. public static boolean containsOnly(String str, String validChars)

### 判断是否字符串str仅包含字符串validChars中的字符，

即字符串str中的字符是否都在字符串validChars中。

和34类似，举例(*表示任意)：


```java
StringUtils.containsOnly(null, *)       = false

StringUtils.containsOnly(*, null)       = false

StringUtils.containsOnly("", "")        = true

StringUtils.containsOnly("", "a")       = true

StringUtils.containsOnly("afaf", "af ") = true

StringUtils.containsOnly("af a", "af ") = true

StringUtils.containsOnly("afg", "af ") = false

StringUtils.containsOnly("afg", "")     = false
```


36. public static boolean containsNone(String str, char[] invalidChars)

### 判断是否字符串str不包含字符数组invalidChars中的字符，如果含有则返回false。

举例(*表示任意)：


```java
StringUtils.containsNone(null, *)               = true

StringUtils.containsNone(*, [])                 = true

StringUtils.containsNone("", *)                 = true

StringUtils.containsNone("ab", [])              = true

StringUtils.containsNone("b", ['a','f',' '])    = true

StringUtils.containsNone("bcd", ['a','f',' ']) = true

StringUtils.containsNone("abc", ['a','f',' ']) = false

StringUtils.containsNone(" ", ['a','f',' '])    = false
```


37. public static boolean containsNone(String str, String invalidChars)

### 判断是否字符串str不包含字符串invalidChars中的字符，如果含有则返回false。

举例(*表示任意)：


```java
StringUtils.containsNone(null, *)       = true

StringUtils.containsNone(*, null)       = true

StringUtils.containsNone("", *)         = true

StringUtils.containsNone("ab", "")      = true

StringUtils.containsNone("b", "af ")    = true

StringUtils.containsNone("bcd", "af ") = true

StringUtils.containsNone("abc", "af ") = false

StringUtils.containsNone(" ", "af ")    = false
```

38. public static int indexOfAny(String str, String[] searchStrs)

### 找出字符串数组searchStrs中的字符串第一次出现在字符串str中的位置。

如果数组中没有字符串在str中，则返回-1

如果数组为null或长度为0，则返回-1

举例(*表示任意)：


```java
StringUtils.indexOfAny(null, *)                     = -1

StringUtils.indexOfAny(*, null)                     = -1

StringUtils.indexOfAny(*, [])                       = -1

StringUtils.indexOfAny("", [""])                    = 0

StringUtils.indexOfAny("bbeegg", ["as","df","yy"]) = -1

StringUtils.indexOfAny("asdfgh", ["as","df","yy"]) = 0

StringUtils.indexOfAny("dfasgh", ["as","df","yy"]) = 0

StringUtils.indexOfAny("ghasdf", ["as","df","yy"]) = 2
```

39. public static int lastIndexOfAny(String str, String[] searchStrs)

### 找出字符串数组searchStrs中的字符串最后一次出现在字符串str中的位置。

如果数组中没有字符串在str中，则返回-1

如果数组为null或长度为0，则返回-1

举例(*表示任意)：


```java
StringUtils.lastIndexOfAny(null, *)                     = -1

StringUtils.lastIndexOfAny(*, null)                     = -1

StringUtils.lastIndexOfAny(*, [])                       = -1

StringUtils.lastIndexOfAny("", [""])                    = 0

StringUtils.lastIndexOfAny("bbeegg", ["as","df","yy"]) = -1

StringUtils.lastIndexOfAny("asdfgh", ["as","df","yy"]) = 2

StringUtils.lastIndexOfAny("dfghjk", ["as","df","yy"]) = 0

StringUtils.lastIndexOfAny("ghasdf", ["as","df","yy"]) = 4

StringUtils.lastIndexOfAny("ghasdf", ["as","df",""])    = 6
```


40. public static String substring(String str, int start)

### 得到字符串str的子串。

如果start小于0，位置是从后往前数的第|start|个

如果str为null或""，则返回它本身

举例(*表示任意)：


```java
StringUtils.substring(null, *)     = null

StringUtils.substring("", *)       = ""

StringUtils.substring("asdf", 0)) = "asdf"

StringUtils.substring("asdf", 1)) = "sdf"

StringUtils.substring("asdf", 3)) = "f"

StringUtils.substring("asdf",) = ""

StringUtils.substring("asdf", -1)) = "f"

StringUtils.substring("asdf", -3)) = "sdf"

StringUtils.substring("asdf", -8)) = "asdf"
```


41. public static String substring(String str, int start, int end)

### 得到字符串str的子串。

如果start小于0，位置是从后往前数的第|start|个，

如果end小于0，位置是从后往前数的第|end|个，

如果str为null或""，则返回它本身

举例(*表示任意)：


```java
StringUtils.substring(null, *, *)     = null

StringUtils.substring("", * , *)     = "";

StringUtils.substring("asdf", 0, 2)   = "as"

StringUtils.substring("asdf", 0, -1) = "asd"

StringUtils.substring("asdf", 2, -1) = "d"

StringUtils.substring("asdf", 2, -2) = ""

StringUtils.substring("asdf", 3, 2)   = ""

StringUtils.substring("asdf", 1,   = "sdf"

StringUtils.substring("asdf", -1, -3) = ""

StringUtils.substring("asdf", -3, -1) = "sd"

StringUtils.substring("asdf", -8, 5) = "asdf"
```


42. public static String left(String str, int len)

### 得到字符串str从左边数len长度的子串。

如果str为null或为""，则返回它本身

如果len小于0，则返回""

举例(*表示任意)：


```java
StringUtils.left(null, *)    = null

StringUtils.left(*, -ve)     = ""

StringUtils.left("", *)      = ""

StringUtils.left("asdf", 0) = ""

StringUtils.left("asdf", 2) = "as"

StringUtils.left("asdf", = "asdf"
```


43. public static String right(String str, int len)

### 得到字符串str从右边数len长度的子串。

如果str为null或为""，则返回它本身

如果len小于0，则返回""

举例(*表示任意)：


```java
StringUtils.right(null, *)    = null

StringUtils.right(*, -ve)     = ""

StringUtils.right("", *)      = ""

StringUtils.right("asdf", 0) = ""

StringUtils.right("asdf", 2) = "df"

StringUtils.right("asdf", = "asdf"
```


44. public static String mid(String str, int pos, int len)

### 得到字符串str从pos开始len长度的子串。

如果str为null或为""，则返回它本身

如果len小于0或pos大于srt的长度，则返回""

如果pos小于0，则pos设为0

举例(*表示任意)：


```java
StringUtils.mid(null, *, *)     = null

StringUtils.mid("", *, *)       = ""

StringUtils.mid(*, *, -ve)      = ""

StringUtils.mid("asdf", 0, 4)) = "asdf"

StringUtils.mid("asdf", 2, 2))    = "df"

StringUtils.mid("asdf", 2, 5))    = "df"

StringUtils.mid("asdf", -2, 1)) = "a"

StringUtils.mid("asdf", 0, -1))    = ""
```


45. public static String substringBefore(String str, String separator)

### 得到字符串str的在字符串separator出现前的字串，且separator不包括在内。

如果str为null或为""，则返回它本身

如果separator为null，则返回str本身

举例(*表示任意)：


```java
StringUtils.substringBefore(null, *)            = null

StringUtils.substringBefore("", *)              = ""

StringUtils.substringBefore("asdfg", null))     = "asdfg"

StringUtils.substringBefore("asdfg", "a"))      = ""

StringUtils.substringBefore("asdfg", "sd"))     = "a"

StringUtils.substringBefore("asdfsag", "sa"))   = "asdf"

StringUtils.substringBefore("asdfg", "h"))      = "asdfg"

StringUtils.substringBefore("asdfg", ""))       = ""

StringUtils.substringBefore("asdfg", "dfgh"))   = "asdfg"

StringUtils.substringBefore("asdfg", "dfg"))    = "as"

StringUtils.substringBefore("abbbabbba", "bb")) = "a"
```


46. public static String substringAfter(String str, String separator)

### 得到字符串str的在字符串separator出现后的字串，且separator不包括在内。

如果str为null或为""，则返回它本身

如果separator为null，则返回""

举例(*表示任意)：


```java
StringUtils.substringAfter(null, *)           = null

StringUtils.substringAfter("", *)             = ""

StringUtils.substringAfter(*, null)           = ""

StringUtils.substringAfter("asdfg", "a"))     = "sdfg"

StringUtils.substringAfter("asdfg", "sd"))        =    "fg"

StringUtils.substringAfter("asdfsag", "sa"))    =    "g"

StringUtils.substringAfter("asdfg", "h"))            =    ""

StringUtils.substringAfter("asdfg", ""))            =    "asdfg"

StringUtils.substringAfter("asdfg", "dfgh"))    =    ""

StringUtils.substringAfter("asdfg", "dfg"))        =    ""

StringUtils.substringAfter("abbbabbba", "bb"))=    "babbba"
```


47. public static String substringBeforeLast(String str, String separator)

### 和45类似，得到字符串str的在字符串separator最后一次出现前的字串。

这里不再举例。

48. public static String substringAfterLast(String str, String separator)

### 和46类似，得到字符串str的在字符串separator最后一次出现后的字串。

这里不再举例。

49. public static String substringBetween(String str, String tag)

### 得到str中的在两个字符串tag中间的字符串，即str中的tag所夹的串。

如果str为null或tag为null，返回null

举例(*表示任意)：


```java
StringUtils.substringBetween(null, *)               = null

StringUtils.substringBetween(*, null)               = null

StringUtils.substringBetween("", "")                = ""

StringUtils.substringBetween("", "a"))              = null

StringUtils.substringBetween("asdfdf", "df"))            =        ""

StringUtils.substringBetween("asdfas", "as"))                = "df"

StringUtils.substringBetween("dfasdfasdfas", "df"))    = "as"

StringUtils.substringBetween("dfasdfasdfas", "as"))    = "df"

StringUtils.substringBetween("dfasdfasgdf", "df")) = "as"
```


50. public static String substringBetween(String str, String open, String close)

### 得到str中的在两个字符串open和close中间的字符串，即open和close所夹的串。

如果str为null或open为null或close为null，返回null

举例(*表示任意)：


```java
StringUtils.substringBetween(null, *, *)             = null

StringUtils.substringBetween(*, null, *)             = null

StringUtils.substringBetween(*, *, null)             = null

StringUtils.substringBetween("", "", "")             = ""

StringUtils.substringBetween("", "", "]")            = null

StringUtils.substringBetween("", "[", "]")           = null

StringUtils.substringBetween("[]", "[","]"))         = ""

StringUtils.substringBetween("a[sd]f", "[","]"))     = "sd"

StringUtils.substringBetween("a[sd]f[gh]", "[","]")) = "sd"

StringUtils.substringBetween("a[sd]f", "]","["))       = null

StringUtils.substringBetween("a[sd]f", "",""))            =    ""
```

51. public static String[] substringsBetween(String str, String open, String close)

### 得到str中的在两个字符串open和close中间的字符串，即open和close所夹的串，

把所有符合的结果放在数组中返回。

和50类似，但是返回了所有的结果(50只返回了第一个匹配的结果)。

这里不再举例。

 

52. public static String[] split(String str)

### 把字符串拆分成一个字符串数组，用空白符(whitespace)作为分隔符。

Whitespace是这样定义的 {@link Character#isWhitespace(char)}

如果字符串为null，返回null

如果字符串为""，返回空数组{}

举例(*表示任意)：


```java
StringUtils.split(null)                   = null

StringUtils.split("")                     = {}

StringUtils.split("as df    yy"))           = {"as","df","yy"}

StringUtils.split(" as df    yy "))          = {"as","df","yy"}

StringUtils.split("as\ndf\ryy"))          = {"as","df","yy"}

StringUtils.split("as\tdf\fyy"))          = {"as","df","yy"}

StringUtils.split("as       df \fyy"))   = {"as","df","yy"}

StringUtils.split("as\t \r df \f \n yy")) = {"as","df","yy"}

StringUtils.split("as"))                  = {"as"}

StringUtils.split(" as "))                = {"as"}
```

53. public static String[] split(String str, char separatorChar)

### 把字符串拆分成一个字符串数组，用指定的字符separatorChar作为分隔符。

如果字符串为null，返回null

如果字符串为""，返回空数组{}

举例(*表示任意)：


```java
StringUtils.split(null, *)           = null

StringUtils.split("", *)             = {}

StringUtils.split("as df yy",' '))   = {"as","df","yy"}       

StringUtils.split(" as df yy ",' ')) = {"as","df","yy"}       

StringUtils.split("asodfoyy",'o'))   = {"as","df","yy"}       

StringUtils.split("as.df.yy",'.'))   = {"as","df","yy"}       

StringUtils.split("as\ndf\nyy",'\n'))= {"as","df","yy"}       

StringUtils.split("as",' '))         = {"as"}       

StringUtils.split(" as ",' '))       = {"as"}
```


54. public static String[] split(String str, String separatorChars)

### 把字符串拆分成一个字符串数组，用指定的字符串separatorChars作为分隔符。

如果字符串str为null，返回null

如果字符串str为""，返回空数组{}

如果separatorChars为null，则默认为空白符

和53类似。

举例(*表示任意)：


```java
StringUtils.split("as \rdf \t yy",null)) = {"as","df","yy"}

StringUtils.split("as\ndf\fyy",null))     = {"as","df","yy"}

StringUtils.split("as",""))               = {"as"}
```

55. public static String[] split(String str, String separatorChars, int max)

### 把字符串拆分成一个字符串数组，用指定的字符串separatorChars作为分隔符，

数组的最大长度为max。

如果字符串str为null，返回null

如果字符串str为""，返回空数组{}

如果separatorChars为null，则默认为空白符

如果max小于等于0，认为是没有限制

举例(*表示任意)：


```java
StringUtils.split(null, *, *)              = null

StringUtils.split("", *, *)                = {}

StringUtils.split("as df yy",null,0))      = {"as","df","yy"}

StringUtils.split("as df yy"," ",0))       = {"as","df","yy"}

StringUtils.split("as.df.yy",".",-1))      = {"as","df","yy"}

StringUtils.split("as.df.yy",".",4))       = {"as","df","yy"}

StringUtils.split("as-!-df-!-yy","-!-",0)) = {"as","df","yy"}

StringUtils.split("as.df.yy",".",2))       = {"as","df.yy"}

StringUtils.split("as","",0))              = {"as"}

StringUtils.split("as","",2))              = {"as"}
```


56. public static String[] splitByWholeSeparator(String str, String separator)

### 个人认为和54功能一样。区别有待发掘。

57. public static String[] splitByWholeSeparator( String str, String separator, int max )

### 个人认为和55功能一样。区别有待发掘。

58. public static String[] splitPreserveAllTokens(String str)

### 把字符串str拆分成一个数组，用空白符(whitespace)作为分隔符，保留所有的标识，

包括相邻分隔符产生的空的标识。它可作为StringTokenizer的一个替代。

Whitespace是这样定义的{@link Character#isWhitespace(char)}。

举例(*表示任意)：


```java
StringUtils.splitPreserveAllTokens(null))             = null

StringUtils.splitPreserveAllTokens(""))               = {}

StringUtils.splitPreserveAllTokens("as df gh jk"))    = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as\ndf\rgh\fjk")) = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as\tdf gh    jk"))   = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as df gh"))      = {"as","","df","gh"}

StringUtils.splitPreserveAllTokens(" as   df "))     = {"","as","","","df","",""}
```


59. public static String[] splitPreserveAllTokens(String str, char separatorChar)

### 和58类似，只是分隔符为字符separatorChar。

举例(*表示任意)：


```java
StringUtils.splitPreserveAllTokens(null, *)            = null

StringUtils.splitPreserveAllTokens("", *)              = {}

StringUtils.splitPreserveAllTokens("as df gh jk",' ')) = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as.df.gh.jk",'.')) = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as..df.gh",'.'))   = {"as","","df","gh"}

StringUtils.splitPreserveAllTokens(",as,,,df,,",',')) = {"","as","","","df","",""}

StringUtils.splitPreserveAllTokens("as.df.gh",','))    = {"as.df.gh"}
```


60. public static String[] splitPreserveAllTokens(String str, String separatorChars)

### 和59类似，只是分隔符为字符串separatorChars。

举例(*表示任意)：


```java
StringUtils.splitPreserveAllTokens(null, *)               = null

StringUtils.splitPreserveAllTokens("", *)                 = {}

StringUtils.splitPreserveAllTokens("as df gh jk",null))   = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as\ndf\rgh\fjk",null))= {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as df gh jk"," "))    = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as.df.gh.jk","."))    = {"as","df","gh","jk"}

StringUtils.splitPreserveAllTokens("as..df.gh","."))      = {"as","","df","gh"}

StringUtils.splitPreserveAllTokens(",as,,,df,,",","))     = {"","as","","","df","",""}

StringUtils.splitPreserveAllTokens("as.df.gh",","))       = {"as.df.gh"}

StringUtils.splitPreserveAllTokens("as.df.gh",""))        = {"as.df.gh"}
```


61. public static String[] splitPreserveAllTokens(String str, String separatorChars, int max)

### 和上面几个类似，只是指定了数组的最大长度。

如果max为0或负数，则认为没有限制。

这里不再举例。

62. public static String join(Object[] array)

### 把数组中的元素连接成一个字符串返回。

举例(*表示任意)：

```java
StringUtils.join(null)                        = null

StringUtils.join({})                          = ""

StringUtils.join({"as","df","gh","jk"}))      = "asdfghjk"

StringUtils.join({"as","","df","gh"}))        = "asdfgh"

StringUtils.join({"","as","","","df","",""})) = "asdf"
```


63. public static String join(Object[] array, char separator)

### 把数组中的元素连接成一个字符串返回，把分隔符separator也加上。

举例(*表示任意)：


```java
StringUtils.join(null, *)                        = null

StringUtils.join({}, *)                          = ""

StringUtils.join({null}, *)                      = ""

StringUtils.join({"as","df","gh","jk"},' '))     = "as df gh jk"

StringUtils.join({"as","df","gh","jk"},'.'))     = "as.df.gh.jk"

StringUtils.join({"as","","df","gh"},'.'))       = "as..df.gh"

StringUtils.join({"","as","","","df","",""},','))= ",as,,,df,,"

StringUtils.join({"","as","","","df","",""},' '))= " as   df "

StringUtils.join({"as.df.gh"},'.'))                 = "as.df.gh"

StringUtils.join({"as.df.gh"},' '))              = "as.df.gh"
```


64. public static String join(Object[] array, char separator, int startIndex, int endIndex)

### 把数组中的元素连接成一个字符串返回，把分隔符separator也加上。

连接的开始位置为startIndex，结束位置为endIndex。

这里不再举例。

65. public static String join(Object[] array, String separator)

### 与63类似，这里不再举例。

66. public static String join(Object[] array, String separator, int startIndex, int endIndex)

### 与64类似，这里不再举例。

 

67. public static String deleteWhitespace(String str)

### 删除字符串中的所有空白符(whitespace)，空白符是这样定义的{@link Character#isWhitespace(char)}。

举例(*表示任意)：


```java
StringUtils.deleteWhitespace(null)            = null

StringUtils.deleteWhitespace("")              = ""

StringUtils.deleteWhitespace("asd"))          = "asd",

StringUtils.deleteWhitespace("as df"))          =    "asdf"

StringUtils.deleteWhitespace("as\n\r\f\tdf"))    =    "asdf"

StringUtils.deleteWhitespace("as\bdf"))            =    "as\bdf"

StringUtils.deleteWhitespace(" as df "))        =    "asdf"
```


68. public static String removeStart(String str, String remove)

### 如果字符串str是以字符串remove开始，则去掉这个开始，然后返回，否则返回原来的串。

举例(*表示任意)：


```java
StringUtils.removeStart(null, *)      = null

StringUtils.removeStart("", *)        = ""

StringUtils.removeStart(*, null)      = *

StringUtils.removeStart("asdf",""))   = "asdf"

StringUtils.removeStart("asdf","as"))    = "df"

StringUtils.removeStart("asdf","df"))    = "asdf"

StringUtils.removeStart("asdf","gh"))    = "asdf"
```


69. public static String removeEnd(String str, String remove)

### 如果字符串str是以字符串remove结尾，则去掉这个结尾，然后返回，否则返回原来的串。

这里不再举例。

70. public static String remove(String str, String remove)

### 去掉字符串str中所有包含remove的部分，然后返回。

这里不再举例。

71. public static String remove(String str, char remove)

去掉字符串str中所有包含remove的部分，然后返回。

这里不再举例。

72. public static String replaceOnce(String text, String repl, String with)

### 在字符串text中用with代替repl，仅一次。

这里不再举例。

73. public static String replace(String text, String repl, String with)

### 在字符串text中用with代替repl，替换所有。

这里不再举例。

74. public static String replace(String text, String repl, String with, int max)

### 在字符串text中用with代替repl，max为最大替换次数。

如果max小于0，则替换所有。

这里不再举例。

75. public static String replaceChars(String str, char searchChar, char replaceChar)

### 在字符串str中用字符replaceChar代替所有字符searchChar，

如果字符串为null或""，则返回它本身。

这里不再举例。

76. public static String replaceChars(String str, String searchChars, String replaceChars)

### 用replaceChars代替str中的searchChars。

replaceChars的长度应该和searchChars的长度相等，

如果replaceChars的长度大于searchChars的长度，超过长度的字符将被忽略，

如果replaceChars的长度小于searchChars的长度，超过长度的字符将被删除。

举例(*表示任意)：


```java
StringUtils.replaceChars(null, *, *)            = null

StringUtils.replaceChars("", *, *)              = ""

StringUtils.replaceChars("asdf", null, *)       = "asdf"

StringUtils.replaceChars("asdf", "", *)         = "asdf"

StringUtils.replaceChars("asdf","s",null))      = "adf"

StringUtils.replaceChars("asdf","s",""))            = "adf"

StringUtils.replaceChars("asdsfsg","s","y"))    = "aydyfyg"

StringUtils.replaceChars("asdf","sd","yy"))        =    "ayyf"

StringUtils.replaceChars("asdf","sd","yyy"))    =    "ayyf"

StringUtils.replaceChars("asssdf","s","yyy"))    =    "ayyydf"

StringUtils.replaceChars("asdf","sd","y"))        = "ayf"

StringUtils.replaceChars("assssddddf","sd","y"))= "ayyyyf"
```


77. public static String overlay(String str, String overlay, int start, int end)

### 用字符串overlay覆盖字符串str从start到end之间的串。

如果str为null，则返回null

如果start或end小于0，则设为0

如果start大于end，则两者交换

如果start或end大于str的长度，则认为等于str的长度

举例(*表示任意)：


```java
StringUtils.overlay(null, *, *, *)        = null

StringUtils.overlay("","as",0,0))         = "as"

StringUtils.overlay("asdfgh","qq",2,5))        =    "asqqh"

StringUtils.overlay("asdfgh","qq",5,2))        =    "asqqh"

StringUtils.overlay("asdfgh","qq",-1,3))    =    "qqfgh"

StringUtils.overlay("asdfgh","qq",-1,-3))    =    "qqasdfgh"

StringUtils.overlay("asdfgh","qq",7,10))    =    "asdfghqq"

StringUtils.overlay("asdfgh","qq",0,8))        =    "qq"

StringUtils.overlay("asdfgh","qq",2,8))        =    "asqq"

StringUtils.overlay("asdfgh",null,2,5))        =    "ash"

StringUtils.overlay("asdfgh","",2,5))            =    "ash"
```


78. public static String chop(String str)

### 去掉字符串str的最后一个字符。

如果字符串以"\r\n"结尾，则去掉它们。

这里不再举例。

79. public static String repeat(String str, int repeat)

### 重复字符串repeat次，组合成一个新串返回。

如果字符串str为null或""，则返回它本身

如果repeat小于0，则返回""

举例(*表示任意)：


```java
StringUtils.repeat(null, *) = null

StringUtils.repeat("", *)   = ""

StringUtils.repeat("a", 3) = "aaa"

StringUtils.repeat("ab", 2) = "abab"

StringUtils.repeat("a", -2) = ""
```


80. public static String rightPad(String str, int size)

### 如果str为null，则返回null

如果字符串长度小于size，则在右边补空格使其长度等于size，然后返回

如果字符串长度大于等于size，则返回它本身

这里不再举例。

81. public static String rightPad(String str, int size, char padChar)

### 和80类似，只是补的字符为padChar。

这里不再举例。

82. public static String rightPad(String str, int size, String padStr)

### 和80类似，只是补的是字符串padStr。

举例(*表示任意)：


```java
StringUtils.rightPad(null, *, *)      = null

StringUtils.rightPad("",0,""))        = ""

StringUtils.rightPad("",3,""))          =    "   "

StringUtils.rightPad("",3,"a"))            =    "aaa"

StringUtils.rightPad("",2,"as"))        =    "as"

StringUtils.rightPad("as",-1,"df"))        =    "as"

StringUtils.rightPad("as",0,"df"))        =    "as"

StringUtils.rightPad("as",3,"df"))        =    "asd"

StringUtils.rightPad("as",8,"df"))        =    "asdfdfdf"

StringUtils.rightPad("as",5,null))        =    "as   "

StringUtils.rightPad("as",5,""))            =    "as   "
```


83. public static String leftPad(String str, int size)

### 和80类似，只是补左边。

这里不再举例。

84. public static String leftPad(String str, int size, char padChar)

### 和81类似。

这里不再举例。

85. public static String leftPad(String str, int size, String padStr)

### 和82类似。

这里不再举例。

86. public static String center(String str, int size)

### 产生一个字符串返回，该字符串长度等于size，str位于新串的中心，其他位置补空格。

如果str为null，则返回null

如果size小于str的长度，则返回str本身

举例(*表示任意)：


```java
StringUtils.center(null, *)   = null

StringUtils.center("",1))     = " "

StringUtils.center("",2))        =    " "

StringUtils.center("as",-1))    =    "as"

StringUtils.center("as",2))        =    "as"

StringUtils.center("as",3))        =    "as "

StringUtils.center("as",4))        =    " as "

StringUtils.center("as",10))    =    "    as    "
```


87. public static String center(String str, int size, char padChar)

### 和86类似，只是其他位置补padChar。

这里不再举例。

88. public static String center(String str, int size, String padStr)

### 和86类似，只是其他位置补padStr。

这里不再举例。

89. public static String swapCase(String str)

### 把字符串中的字符大写转换为小写，小写转换为大写。

举例：


```java
StringUtils.swapCase(null)          = null

StringUtils.swapCase("")            = ""

StringUtils.swapCase("Hello Boys")) = "hELLO bOYS"

StringUtils.swapCase("I am 11"))        =    "i AM 11"
```


90. public static int countMatches(String str, String sub)

### 计算字符串sub在字符串str中出现的次数。

如果str为null或""，则返回0

举例(*表示任意)：


```java
StringUtils.countMatches(null, *)        = 0

StringUtils.countMatches("", *)          = 0

StringUtils.countMatches("asdf","as"))   = 1

StringUtils.countMatches("asdfas","as")) = 2

StringUtils.countMatches("dfgh","as"))   = 0

StringUtils.countMatches("as",""))            = 0

StringUtils.countMatches("as",null))        = 0
```
# DateUtil

### DateUtil类
```java

package com.util;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class DateUtil {

	/**
	 * 获取SimpleDateFormat
	 * @param parttern 日期格式
	 * @return SimpleDateFormat对象
	 * @throws RuntimeException 异常：非法日期格式
	 */
	private static SimpleDateFormat getDateFormat(String parttern) throws RuntimeException {
		return new SimpleDateFormat(parttern);
	}

	/**
	 * 获取日期中的某数值。如获取月份
	 * @param date 日期
	 * @param dateType 日期格式
	 * @return 数值
	 */
	private static int getInteger(Date date, int dateType) {
		Calendar calendar = Calendar.getInstance();
		calendar.setTime(date);
		return calendar.get(dateType);
	}
	
	/**
	 * 增加日期中某类型的某数值。如增加日期
	 * @param date 日期字符串
	 * @param dateType 类型
	 * @param amount 数值
	 * @return 计算后日期字符串
	 */
	private static String addInteger(String date, int dateType, int amount) {
		String dateString = null;
		DateStyle dateStyle = getDateStyle(date);
		if (dateStyle != null) {
			Date myDate = StringToDate(date, dateStyle);
			myDate = addInteger(myDate, dateType, amount);
			dateString = DateToString(myDate, dateStyle);
		}
		return dateString;
	}
	
	/**
	 * 增加日期中某类型的某数值。如增加日期
	 * @param date 日期
	 * @param dateType 类型
	 * @param amount 数值
	 * @return 计算后日期
	 */
	private static Date addInteger(Date date, int dateType, int amount) {
		Date myDate = null;
		if (date != null) {
			Calendar calendar = Calendar.getInstance();
			calendar.setTime(date);
			calendar.add(dateType, amount);
			myDate = calendar.getTime();
		}
		return myDate;
	}

	/**
	 * 获取精确的日期
	 * @param timestamps 时间long集合
	 * @return 日期
	 */
	private static Date getAccurateDate(List<Long> timestamps) {
		Date date = null;
		long timestamp = 0;
		Map<Long, long[]> map = new HashMap<Long, long[]>();
		List<Long> absoluteValues = new ArrayList<Long>();

		if (timestamps != null && timestamps.size() > 0) {
			if (timestamps.size() > 1) {
				for (int i = 0; i < timestamps.size(); i++) {
					for (int j = i + 1; j < timestamps.size(); j++) {
						long absoluteValue = Math.abs(timestamps.get(i) - timestamps.get(j));
						absoluteValues.add(absoluteValue);
						long[] timestampTmp = { timestamps.get(i), timestamps.get(j) };
						map.put(absoluteValue, timestampTmp);
					}
				}

				// 有可能有相等的情况。如2012-11和2012-11-01。时间戳是相等的
				long minAbsoluteValue = -1;
				if (!absoluteValues.isEmpty()) {
					// 如果timestamps的size为2，这是差值只有一个，因此要给默认值
					minAbsoluteValue = absoluteValues.get(0);
				}
				for (int i = 0; i < absoluteValues.size(); i++) {
					for (int j = i + 1; j < absoluteValues.size(); j++) {
						if (absoluteValues.get(i) > absoluteValues.get(j)) {
							minAbsoluteValue = absoluteValues.get(j);
						} else {
							minAbsoluteValue = absoluteValues.get(i);
						}
					}
				}

				if (minAbsoluteValue != -1) {
					long[] timestampsLastTmp = map.get(minAbsoluteValue);
					if (absoluteValues.size() > 1) {
						timestamp = Math.max(timestampsLastTmp[0], timestampsLastTmp[1]);
					} else if (absoluteValues.size() == 1) {
						// 当timestamps的size为2，需要与当前时间作为参照
						long dateOne = timestampsLastTmp[0];
						long dateTwo = timestampsLastTmp[1];
						if ((Math.abs(dateOne - dateTwo)) < 100000000000L) {
							timestamp = Math.max(timestampsLastTmp[0], timestampsLastTmp[1]);
						} else {
							long now = new Date().getTime();
							if (Math.abs(dateOne - now) <= Math.abs(dateTwo - now)) {
								timestamp = dateOne;
							} else {
								timestamp = dateTwo;
							}
						}
					}
				}
			} else {
				timestamp = timestamps.get(0);
			}
		}

		if (timestamp != 0) {
			date = new Date(timestamp);
		}
		return date;
	}

	/**
	 * 判断字符串是否为日期字符串
	 * @param date 日期字符串
	 * @return true or false
	 */
	public static boolean isDate(String date) {
		boolean isDate = false;
		if (date != null) {
			if (StringToDate(date) != null) {
				isDate = true;
			}
		}
		return isDate;
	}

	/**
	 * 获取日期字符串的日期风格。失敗返回null。
	 * @param date 日期字符串
	 * @return 日期风格
	 */
	public static DateStyle getDateStyle(String date) {
		DateStyle dateStyle = null;
		Map<Long, DateStyle> map = new HashMap<Long, DateStyle>();
		List<Long> timestamps = new ArrayList<Long>();
		for (DateStyle style : DateStyle.values()) {
			Date dateTmp = StringToDate(date, style.getValue());
			if (dateTmp != null) {
				timestamps.add(dateTmp.getTime());
				map.put(dateTmp.getTime(), style);
			}
		}
		dateStyle = map.get(getAccurateDate(timestamps).getTime());
		return dateStyle;
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @return 日期
	 */
	public static Date StringToDate(String date) {
		DateStyle dateStyle = null;
		return StringToDate(date, dateStyle);
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @param parttern 日期格式
	 * @return 日期
	 */
	public static Date StringToDate(String date, String parttern) {
		Date myDate = null;
		if (date != null) {
			try {
				myDate = getDateFormat(parttern).parse(date);
			} catch (Exception e) {
			}
		}
		return myDate;
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @param dateStyle 日期风格
	 * @return 日期
	 */
	public static Date StringToDate(String date, DateStyle dateStyle) {
		Date myDate = null;
		if (dateStyle == null) {
			List<Long> timestamps = new ArrayList<Long>();
			for (DateStyle style : DateStyle.values()) {
				Date dateTmp = StringToDate(date, style.getValue());
				if (dateTmp != null) {
					timestamps.add(dateTmp.getTime());
				}
			}
			myDate = getAccurateDate(timestamps);
		} else {
			myDate = StringToDate(date, dateStyle.getValue());
		}
		return myDate;
	}

	/**
	 * 将日期转化为日期字符串。失败返回null。
	 * @param date 日期
	 * @param parttern 日期格式
	 * @return 日期字符串
	 */
	public static String DateToString(Date date, String parttern) {
		String dateString = null;
		if (date != null) {
			try {
				dateString = getDateFormat(parttern).format(date);
			} catch (Exception e) {
			}
		}
		return dateString;
	}

	/**
	 * 将日期转化为日期字符串。失败返回null。
	 * @param date 日期
	 * @param dateStyle 日期风格
	 * @return 日期字符串
	 */
	public static String DateToString(Date date, DateStyle dateStyle) {
		String dateString = null;
		if (dateStyle != null) {
			dateString = DateToString(date, dateStyle.getValue());
		}
		return dateString;
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param parttern 新日期格式
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, String parttern) {
		return StringToString(date, null, parttern);
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param dateStyle 新日期风格
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, DateStyle dateStyle) {
		return StringToString(date, null, dateStyle);
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddParttern 旧日期格式
	 * @param newParttern 新日期格式
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, String olddParttern, String newParttern) {
		String dateString = null;
		if (olddParttern == null) {
			DateStyle style = getDateStyle(date);
			if (style != null) {
				Date myDate = StringToDate(date, style.getValue());
				dateString = DateToString(myDate, newParttern);
			}
		} else {
			Date myDate = StringToDate(date, olddParttern);
			dateString = DateToString(myDate, newParttern);
		}
		return dateString;
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddDteStyle 旧日期风格
	 * @param newDateStyle 新日期风格
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, DateStyle olddDteStyle, DateStyle newDateStyle) {
		String dateString = null;
		if (olddDteStyle == null) {
			DateStyle style = getDateStyle(date);
			dateString = StringToString(date, style.getValue(), newDateStyle.getValue());
		} else {
			dateString = StringToString(date, olddDteStyle.getValue(), newDateStyle.getValue());
		}
		return dateString;
	}

	/**
	 * 增加日期的年份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加年份后的日期字符串
	 */
	public static String addYear(String date, int yearAmount) {
		return addInteger(date, Calendar.YEAR, yearAmount);
	}
	
	/**
	 * 增加日期的年份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加年份后的日期
	 */
	public static Date addYear(Date date, int yearAmount) {
		return addInteger(date, Calendar.YEAR, yearAmount);
	}
	
	/**
	 * 增加日期的月份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加月份后的日期字符串
	 */
	public static String addMonth(String date, int yearAmount) {
		return addInteger(date, Calendar.MONTH, yearAmount);
	}
	
	/**
	 * 增加日期的月份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加月份后的日期
	 */
	public static Date addMonth(Date date, int yearAmount) {
		return addInteger(date, Calendar.MONTH, yearAmount);
	}
	
	/**
	 * 增加日期的天数。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加天数后的日期字符串
	 */
	public static String addDay(String date, int dayAmount) {
		return addInteger(date, Calendar.DATE, dayAmount);
	}

	/**
	 * 增加日期的天数。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加天数后的日期
	 */
	public static Date addDay(Date date, int dayAmount) {
		return addInteger(date, Calendar.DATE, dayAmount);
	}
	
	/**
	 * 增加日期的小时。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加小时后的日期字符串
	 */
	public static String addHour(String date, int hourAmount) {
		return addInteger(date, Calendar.HOUR_OF_DAY, hourAmount);
	}

	/**
	 * 增加日期的小时。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加小时后的日期
	 */
	public static Date addHour(Date date, int hourAmount) {
		return addInteger(date, Calendar.HOUR_OF_DAY, hourAmount);
	}
	
	/**
	 * 增加日期的分钟。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加分钟后的日期字符串
	 */
	public static String addMinute(String date, int hourAmount) {
		return addInteger(date, Calendar.MINUTE, hourAmount);
	}

	/**
	 * 增加日期的分钟。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加分钟后的日期
	 */
	public static Date addMinute(Date date, int hourAmount) {
		return addInteger(date, Calendar.MINUTE, hourAmount);
	}
	
	/**
	 * 增加日期的秒钟。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加秒钟后的日期字符串
	 */
	public static String addSecond(String date, int hourAmount) {
		return addInteger(date, Calendar.SECOND, hourAmount);
	}

	/**
	 * 增加日期的秒钟。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加秒钟后的日期
	 */
	public static Date addSecond(Date date, int hourAmount) {
		return addInteger(date, Calendar.SECOND, hourAmount);
	}

	/**
	 * 获取日期的年份。失败返回0。
	 * @param date 日期字符串
	 * @return 年份
	 */
	public static int getYear(String date) {
		return getYear(StringToDate(date));
	}

	/**
	 * 获取日期的年份。失败返回0。
	 * @param date 日期
	 * @return 年份
	 */
	public static int getYear(Date date) {
		return getInteger(date, Calendar.YEAR);
	}

	/**
	 * 获取日期的月份。失败返回0。
	 * @param date 日期字符串
	 * @return 月份
	 */
	public static int getMonth(String date) {
		return getMonth(StringToDate(date));
	}

	/**
	 * 获取日期的月份。失败返回0。
	 * @param date 日期
	 * @return 月份
	 */
	public static int getMonth(Date date) {
		return getInteger(date, Calendar.MONTH);
	}

	/**
	 * 获取日期的天数。失败返回0。
	 * @param date 日期字符串
	 * @return 天
	 */
	public static int getDay(String date) {
		return getDay(StringToDate(date));
	}

	/**
	 * 获取日期的天数。失败返回0。
	 * @param date 日期
	 * @return 天
	 */
	public static int getDay(Date date) {
		return getInteger(date, Calendar.DATE);
	}
	
	/**
	 * 获取日期的小时。失败返回0。
	 * @param date 日期字符串
	 * @return 小时
	 */
	public static int getHour(String date) {
		return getHour(StringToDate(date));
	}

	/**
	 * 获取日期的小时。失败返回0。
	 * @param date 日期
	 * @return 小时
	 */
	public static int getHour(Date date) {
		return getInteger(date, Calendar.HOUR_OF_DAY);
	}
	
	/**
	 * 获取日期的分钟。失败返回0。
	 * @param date 日期字符串
	 * @return 分钟
	 */
	public static int getMinute(String date) {
		return getMinute(StringToDate(date));
	}

	/**
	 * 获取日期的分钟。失败返回0。
	 * @param date 日期
	 * @return 分钟
	 */
	public static int getMinute(Date date) {
		return getInteger(date, Calendar.MINUTE);
	}
	
	/**
	 * 获取日期的秒钟。失败返回0。
	 * @param date 日期字符串
	 * @return 秒钟
	 */
	public static int getSecond(String date) {
		return getSecond(StringToDate(date));
	}

	/**
	 * 获取日期的秒钟。失败返回0。
	 * @param date 日期
	 * @return 秒钟
	 */
	public static int getSecond(Date date) {
		return getInteger(date, Calendar.SECOND);
	}

	/**
	 * 获取日期 。默认yyyy-MM-dd格式。失败返回null。
	 * @param date 日期字符串
	 * @return 日期
	 */
	public static String getDate(String date) {
		return StringToString(date, DateStyle.YYYY_MM_DD);
	}

	/**
	 * 获取日期。默认yyyy-MM-dd格式。失败返回null。
	 * @param date 日期
	 * @return 日期
	 */
	public static String getDate(Date date) {
		return DateToString(date, DateStyle.YYYY_MM_DD);
	}

	/**
	 * 获取日期的时间。默认HH:mm:ss格式。失败返回null。
	 * @param date 日期字符串
	 * @return 时间
	 */
	public static String getTime(String date) {
		return StringToString(date, DateStyle.HH_MM_SS);
	}

	/**
	 * 获取日期的时间。默认HH:mm:ss格式。失败返回null。
	 * @param date 日期
	 * @return 时间
	 */
	public static String getTime(Date date) {
		return DateToString(date, DateStyle.HH_MM_SS);
	}

	/**
	 * 获取日期的星期。失败返回null。
	 * @param date 日期字符串
	 * @return 星期
	 */
	public static Week getWeek(String date) {
		Week week = null;
		DateStyle dateStyle = getDateStyle(date);
		if (dateStyle != null) {
			Date myDate = StringToDate(date, dateStyle);
			week = getWeek(myDate);
		}
		return week;
	}

	/**
	 * 获取日期的星期。失败返回null。
	 * @param date 日期
	 * @return 星期
	 */
	public static Week getWeek(Date date) {
		Week week = null;
		Calendar calendar = Calendar.getInstance();
		calendar.setTime(date);
		int weekNumber = calendar.get(Calendar.DAY_OF_WEEK) - 1;
		switch (weekNumber) {
		case 0:
			week = Week.SUNDAY;
			break;
		case 1:
			week = Week.MONDAY;
			break;
		case 2:
			week = Week.TUESDAY;
			break;
		case 3:
			week = Week.WEDNESDAY;
			break;
		case 4:
			week = Week.THURSDAY;
			break;
		case 5:
			week = Week.FRIDAY;
			break;
		case 6:
			week = Week.SATURDAY;
			break;
		}
		return week;
	}
	
	/**
	 * 获取两个日期相差的天数
	 * @param date 日期字符串
	 * @param otherDate 另一个日期字符串
	 * @return 相差天数
	 */
	public static int getIntervalDays(String date, String otherDate) {
		return getIntervalDays(StringToDate(date), StringToDate(otherDate));
	}
	
	/**
	 * @param date 日期
	 * @param otherDate 另一个日期
	 * @return 相差天数
	 */
	public static int getIntervalDays(Date date, Date otherDate) {
		date = DateUtil.StringToDate(DateUtil.getDate(date));
		long time = Math.abs(date.getTime() - otherDate.getTime());
		return (int)time/(24 * 60 * 60 * 1000);
	}
}
```

### DateStyle类

```java

package com.util;

public enum DateStyle {
	
	MM_DD("MM-dd"),
	YYYY_MM("yyyy-MM"),
	YYYY_MM_DD("yyyy-MM-dd"),
	MM_DD_HH_MM("MM-dd HH:mm"),
	MM_DD_HH_MM_SS("MM-dd HH:mm:ss"),
	YYYY_MM_DD_HH_MM("yyyy-MM-dd HH:mm"),
	YYYY_MM_DD_HH_MM_SS("yyyy-MM-dd HH:mm:ss"),
	
	MM_DD_EN("MM/dd"),
	YYYY_MM_EN("yyyy/MM"),
	YYYY_MM_DD_EN("yyyy/MM/dd"),
	MM_DD_HH_MM_EN("MM/dd HH:mm"),
	MM_DD_HH_MM_SS_EN("MM/dd HH:mm:ss"),
	YYYY_MM_DD_HH_MM_EN("yyyy/MM/dd HH:mm"),
	YYYY_MM_DD_HH_MM_SS_EN("yyyy/MM/dd HH:mm:ss"),
	
	MM_DD_CN("MM月dd日"),
	YYYY_MM_CN("yyyy年MM月"),
	YYYY_MM_DD_CN("yyyy年MM月dd日"),
	MM_DD_HH_MM_CN("MM月dd日 HH:mm"),
	MM_DD_HH_MM_SS_CN("MM月dd日 HH:mm:ss"),
	YYYY_MM_DD_HH_MM_CN("yyyy年MM月dd日 HH:mm"),
	YYYY_MM_DD_HH_MM_SS_CN("yyyy年MM月dd日 HH:mm:ss"),
	
	HH_MM("HH:mm"),
	HH_MM_SS("HH:mm:ss");
	
	
	private String value;
	
	DateStyle(String value) {
		this.value = value;
	}
	
	public String getValue() {
		return value;
	}
}

```

### Week类

```java

package com.util;

public enum Week {

	MONDAY("星期一", "Monday", "Mon.", 1),
	TUESDAY("星期二", "Tuesday", "Tues.", 2),
	WEDNESDAY("星期三", "Wednesday", "Wed.", 3),
	THURSDAY("星期四", "Thursday", "Thur.", 4),
	FRIDAY("星期五", "Friday", "Fri.", 5),
	SATURDAY("星期六", "Saturday", "Sat.", 6),
	SUNDAY("星期日", "Sunday", "Sun.", 7);
	
	String name_cn;
	String name_en;
	String name_enShort;
	int number;
	
	Week(String name_cn, String name_en, String name_enShort, int number) {
		this.name_cn = name_cn;
		this.name_en = name_en;
		this.name_enShort = name_enShort;
		this.number = number;
	}
	
	public String getChineseName() {
		return name_cn;
	}

	public String getName() {
		return name_en;
	}

	public String getShortName() {
		return name_enShort;
	}

	public int getNumber() {
		return number;
	}
}

```
## DateUtils优化版：

1. 修正当字符串日期风格为MM-dd或yyyy-MM时，若日期太大或太小后，识别日期错误。

2. 修正识别日期算法（getAccurateDate）bug。

3. 修正计算日期天数差（getIntervalDays）bug。

4. 优化DateUtil处理日期的速度。

5. 优化日期风格（DateStyle）识别方式。

### DateUtils
```java

package com.itkt.mtravel.hotel.util;

import java.text.ParsePosition;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class DateUtil {

	private static final ThreadLocal<SimpleDateFormat> threadLocal = new ThreadLocal<SimpleDateFormat>();
	
	private static final Object object = new Object();
	
	/**
	 * 获取SimpleDateFormat
	 * @param pattern 日期格式
	 * @return SimpleDateFormat对象
	 * @throws RuntimeException 异常：非法日期格式
	 */
	private static SimpleDateFormat getDateFormat(String pattern) throws RuntimeException {
		SimpleDateFormat dateFormat = threadLocal.get();
		if (dateFormat == null) {
			synchronized (object) {
				if (dateFormat == null) {
					dateFormat = new SimpleDateFormat(pattern);
					dateFormat.setLenient(false);
					threadLocal.set(dateFormat);
				}
			}
		}
		dateFormat.applyPattern(pattern);
		return dateFormat;
	}

	/**
	 * 获取日期中的某数值。如获取月份
	 * @param date 日期
	 * @param dateType 日期格式
	 * @return 数值
	 */
	private static int getInteger(Date date, int dateType) {
		int num = 0;
		Calendar calendar = Calendar.getInstance();
		if (date != null) {
			calendar.setTime(date);
			num = calendar.get(dateType);
		}
		return num;
	}

	/**
	 * 增加日期中某类型的某数值。如增加日期
	 * @param date 日期字符串
	 * @param dateType 类型
	 * @param amount 数值
	 * @return 计算后日期字符串
	 */
	private static String addInteger(String date, int dateType, int amount) {
		String dateString = null;
		DateStyle dateStyle = getDateStyle(date);
		if (dateStyle != null) {
			Date myDate = StringToDate(date, dateStyle);
			myDate = addInteger(myDate, dateType, amount);
			dateString = DateToString(myDate, dateStyle);
		}
		return dateString;
	}

	/**
	 * 增加日期中某类型的某数值。如增加日期
	 * @param date 日期
	 * @param dateType 类型
	 * @param amount 数值
	 * @return 计算后日期
	 */
	private static Date addInteger(Date date, int dateType, int amount) {
		Date myDate = null;
		if (date != null) {
			Calendar calendar = Calendar.getInstance();
			calendar.setTime(date);
			calendar.add(dateType, amount);
			myDate = calendar.getTime();
		}
		return myDate;
	}

	/**
	 * 获取精确的日期
	 * @param timestamps 时间long集合
	 * @return 日期
	 */
	private static Date getAccurateDate(List<Long> timestamps) {
		Date date = null;
		long timestamp = 0;
		Map<Long, long[]> map = new HashMap<Long, long[]>();
		List<Long> absoluteValues = new ArrayList<Long>();

		if (timestamps != null && timestamps.size() > 0) {
			if (timestamps.size() > 1) {
				for (int i = 0; i < timestamps.size(); i++) {
					for (int j = i + 1; j < timestamps.size(); j++) {
						long absoluteValue = Math.abs(timestamps.get(i) - timestamps.get(j));
						absoluteValues.add(absoluteValue);
						long[] timestampTmp = { timestamps.get(i), timestamps.get(j) };
						map.put(absoluteValue, timestampTmp);
					}
				}

				// 有可能有相等的情况。如2012-11和2012-11-01。时间戳是相等的。此时minAbsoluteValue为0
				// 因此不能将minAbsoluteValue取默认值0
				long minAbsoluteValue = -1;
				if (!absoluteValues.isEmpty()) {
					minAbsoluteValue = absoluteValues.get(0);
					for (int i = 1; i < absoluteValues.size(); i++) {
						if (minAbsoluteValue > absoluteValues.get(i)) {
							minAbsoluteValue = absoluteValues.get(i);
						}
					}
				}

				if (minAbsoluteValue != -1) {
					long[] timestampsLastTmp = map.get(minAbsoluteValue);

					long dateOne = timestampsLastTmp[0];
					long dateTwo = timestampsLastTmp[1];
					if (absoluteValues.size() > 1) {
						timestamp = Math.abs(dateOne) > Math.abs(dateTwo) ? dateOne : dateTwo;
					}
				}
			} else {
				timestamp = timestamps.get(0);
			}
		}

		if (timestamp != 0) {
			date = new Date(timestamp);
		}
		return date;
	}

	/**
	 * 判断字符串是否为日期字符串
	 * @param date 日期字符串
	 * @return true or false
	 */
	public static boolean isDate(String date) {
		boolean isDate = false;
		if (date != null) {
			if (getDateStyle(date) != null) {
				isDate = true;
			}
		}
		return isDate;
	}

	/**
	 * 获取日期字符串的日期风格。失敗返回null。
	 * @param date 日期字符串
	 * @return 日期风格
	 */
	public static DateStyle getDateStyle(String date) {
		DateStyle dateStyle = null;
		Map<Long, DateStyle> map = new HashMap<Long, DateStyle>();
		List<Long> timestamps = new ArrayList<Long>();
		for (DateStyle style : DateStyle.values()) {
			if (style.isShowOnly()) {
				continue;
			}
			Date dateTmp = null;
			if (date != null) {
				try {
					ParsePosition pos = new ParsePosition(0);
					dateTmp = getDateFormat(style.getValue()).parse(date, pos);
					if (pos.getIndex() != date.length()) {
						dateTmp = null;
					}
				} catch (Exception e) {
				}
			}
			if (dateTmp != null) {
				timestamps.add(dateTmp.getTime());
				map.put(dateTmp.getTime(), style);
			}
		}
		Date accurateDate = getAccurateDate(timestamps);
		if (accurateDate != null) {
			dateStyle = map.get(accurateDate.getTime());
		}
		return dateStyle;
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @return 日期
	 */
	public static Date StringToDate(String date) {
		DateStyle dateStyle = getDateStyle(date);
		return StringToDate(date, dateStyle);
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @param pattern 日期格式
	 * @return 日期
	 */
	public static Date StringToDate(String date, String pattern) {
		Date myDate = null;
		if (date != null) {
			try {
				myDate = getDateFormat(pattern).parse(date);
			} catch (Exception e) {
			}
		}
		return myDate;
	}

	/**
	 * 将日期字符串转化为日期。失败返回null。
	 * @param date 日期字符串
	 * @param dateStyle 日期风格
	 * @return 日期
	 */
	public static Date StringToDate(String date, DateStyle dateStyle) {
		Date myDate = null;
		if (dateStyle != null) {
			myDate = StringToDate(date, dateStyle.getValue());
		}
		return myDate;
	}

	/**
	 * 将日期转化为日期字符串。失败返回null。
	 * @param date 日期
	 * @param pattern 日期格式
	 * @return 日期字符串
	 */
	public static String DateToString(Date date, String pattern) {
		String dateString = null;
		if (date != null) {
			try {
				dateString = getDateFormat(pattern).format(date);
			} catch (Exception e) {
			}
		}
		return dateString;
	}

	/**
	 * 将日期转化为日期字符串。失败返回null。
	 * @param date 日期
	 * @param dateStyle 日期风格
	 * @return 日期字符串
	 */
	public static String DateToString(Date date, DateStyle dateStyle) {
		String dateString = null;
		if (dateStyle != null) {
			dateString = DateToString(date, dateStyle.getValue());
		}
		return dateString;
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param newPattern 新日期格式
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, String newPattern) {
		DateStyle oldDateStyle = getDateStyle(date);
		return StringToString(date, oldDateStyle, newPattern);
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param newDateStyle 新日期风格
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, DateStyle newDateStyle) {
		DateStyle oldDateStyle = getDateStyle(date);
		return StringToString(date, oldDateStyle, newDateStyle);
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddPattern 旧日期格式
	 * @param newPattern 新日期格式
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, String olddPattern, String newPattern) {
		return DateToString(StringToDate(date, olddPattern), newPattern);
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddDteStyle 旧日期风格
	 * @param newParttern 新日期格式
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, DateStyle olddDteStyle, String newParttern) {
		String dateString = null;
		if (olddDteStyle != null) {
			dateString = StringToString(date, olddDteStyle.getValue(), newParttern);
		}
		return dateString;
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddPattern 旧日期格式
	 * @param newDateStyle 新日期风格
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, String olddPattern, DateStyle newDateStyle) {
		String dateString = null;
		if (newDateStyle != null) {
			dateString = StringToString(date, olddPattern, newDateStyle.getValue());
		}
		return dateString;
	}

	/**
	 * 将日期字符串转化为另一日期字符串。失败返回null。
	 * @param date 旧日期字符串
	 * @param olddDteStyle 旧日期风格
	 * @param newDateStyle 新日期风格
	 * @return 新日期字符串
	 */
	public static String StringToString(String date, DateStyle olddDteStyle, DateStyle newDateStyle) {
		String dateString = null;
		if (olddDteStyle != null && newDateStyle != null) {
			dateString = StringToString(date, olddDteStyle.getValue(), newDateStyle.getValue());
		}
		return dateString;
	}

	/**
	 * 增加日期的年份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加年份后的日期字符串
	 */
	public static String addYear(String date, int yearAmount) {
		return addInteger(date, Calendar.YEAR, yearAmount);
	}

	/**
	 * 增加日期的年份。失败返回null。
	 * @param date 日期
	 * @param yearAmount 增加数量。可为负数
	 * @return 增加年份后的日期
	 */
	public static Date addYear(Date date, int yearAmount) {
		return addInteger(date, Calendar.YEAR, yearAmount);
	}

	/**
	 * 增加日期的月份。失败返回null。
	 * @param date 日期
	 * @param monthAmount 增加数量。可为负数
	 * @return 增加月份后的日期字符串
	 */
	public static String addMonth(String date, int monthAmount) {
		return addInteger(date, Calendar.MONTH, monthAmount);
	}

	/**
	 * 增加日期的月份。失败返回null。
	 * @param date 日期
	 * @param monthAmount 增加数量。可为负数
	 * @return 增加月份后的日期
	 */
	public static Date addMonth(Date date, int monthAmount) {
		return addInteger(date, Calendar.MONTH, monthAmount);
	}

	/**
	 * 增加日期的天数。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加天数后的日期字符串
	 */
	public static String addDay(String date, int dayAmount) {
		return addInteger(date, Calendar.DATE, dayAmount);
	}

	/**
	 * 增加日期的天数。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加天数后的日期
	 */
	public static Date addDay(Date date, int dayAmount) {
		return addInteger(date, Calendar.DATE, dayAmount);
	}

	/**
	 * 增加日期的小时。失败返回null。
	 * @param date 日期字符串
	 * @param hourAmount 增加数量。可为负数
	 * @return 增加小时后的日期字符串
	 */
	public static String addHour(String date, int hourAmount) {
		return addInteger(date, Calendar.HOUR_OF_DAY, hourAmount);
	}

	/**
	 * 增加日期的小时。失败返回null。
	 * @param date 日期
	 * @param hourAmount 增加数量。可为负数
	 * @return 增加小时后的日期
	 */
	public static Date addHour(Date date, int hourAmount) {
		return addInteger(date, Calendar.HOUR_OF_DAY, hourAmount);
	}

	/**
	 * 增加日期的分钟。失败返回null。
	 * @param date 日期字符串
	 * @param minuteAmount 增加数量。可为负数
	 * @return 增加分钟后的日期字符串
	 */
	public static String addMinute(String date, int minuteAmount) {
		return addInteger(date, Calendar.MINUTE, minuteAmount);
	}

	/**
	 * 增加日期的分钟。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加分钟后的日期
	 */
	public static Date addMinute(Date date, int minuteAmount) {
		return addInteger(date, Calendar.MINUTE, minuteAmount);
	}

	/**
	 * 增加日期的秒钟。失败返回null。
	 * @param date 日期字符串
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加秒钟后的日期字符串
	 */
	public static String addSecond(String date, int secondAmount) {
		return addInteger(date, Calendar.SECOND, secondAmount);
	}

	/**
	 * 增加日期的秒钟。失败返回null。
	 * @param date 日期
	 * @param dayAmount 增加数量。可为负数
	 * @return 增加秒钟后的日期
	 */
	public static Date addSecond(Date date, int secondAmount) {
		return addInteger(date, Calendar.SECOND, secondAmount);
	}

	/**
	 * 获取日期的年份。失败返回0。
	 * @param date 日期字符串
	 * @return 年份
	 */
	public static int getYear(String date) {
		return getYear(StringToDate(date));
	}

	/**
	 * 获取日期的年份。失败返回0。
	 * @param date 日期
	 * @return 年份
	 */
	public static int getYear(Date date) {
		return getInteger(date, Calendar.YEAR);
	}

	/**
	 * 获取日期的月份。失败返回0。
	 * @param date 日期字符串
	 * @return 月份
	 */
	public static int getMonth(String date) {
		return getMonth(StringToDate(date));
	}

	/**
	 * 获取日期的月份。失败返回0。
	 * @param date 日期
	 * @return 月份
	 */
	public static int getMonth(Date date) {
		return getInteger(date, Calendar.MONTH) + 1;
	}

	/**
	 * 获取日期的天数。失败返回0。
	 * @param date 日期字符串
	 * @return 天
	 */
	public static int getDay(String date) {
		return getDay(StringToDate(date));
	}

	/**
	 * 获取日期的天数。失败返回0。
	 * @param date 日期
	 * @return 天
	 */
	public static int getDay(Date date) {
		return getInteger(date, Calendar.DATE);
	}

	/**
	 * 获取日期的小时。失败返回0。
	 * @param date 日期字符串
	 * @return 小时
	 */
	public static int getHour(String date) {
		return getHour(StringToDate(date));
	}

	/**
	 * 获取日期的小时。失败返回0。
	 * @param date 日期
	 * @return 小时
	 */
	public static int getHour(Date date) {
		return getInteger(date, Calendar.HOUR_OF_DAY);
	}

	/**
	 * 获取日期的分钟。失败返回0。
	 * @param date 日期字符串
	 * @return 分钟
	 */
	public static int getMinute(String date) {
		return getMinute(StringToDate(date));
	}

	/**
	 * 获取日期的分钟。失败返回0。
	 * @param date 日期
	 * @return 分钟
	 */
	public static int getMinute(Date date) {
		return getInteger(date, Calendar.MINUTE);
	}

	/**
	 * 获取日期的秒钟。失败返回0。
	 * @param date 日期字符串
	 * @return 秒钟
	 */
	public static int getSecond(String date) {
		return getSecond(StringToDate(date));
	}

	/**
	 * 获取日期的秒钟。失败返回0。
	 * @param date 日期
	 * @return 秒钟
	 */
	public static int getSecond(Date date) {
		return getInteger(date, Calendar.SECOND);
	}

	/**
	 * 获取日期 。默认yyyy-MM-dd格式。失败返回null。
	 * @param date 日期字符串
	 * @return 日期
	 */
	public static String getDate(String date) {
		return StringToString(date, DateStyle.YYYY_MM_DD);
	}

	/**
	 * 获取日期。默认yyyy-MM-dd格式。失败返回null。
	 * @param date 日期
	 * @return 日期
	 */
	public static String getDate(Date date) {
		return DateToString(date, DateStyle.YYYY_MM_DD);
	}

	/**
	 * 获取日期的时间。默认HH:mm:ss格式。失败返回null。
	 * @param date 日期字符串
	 * @return 时间
	 */
	public static String getTime(String date) {
		return StringToString(date, DateStyle.HH_MM_SS);
	}

	/**
	 * 获取日期的时间。默认HH:mm:ss格式。失败返回null。
	 * @param date 日期
	 * @return 时间
	 */
	public static String getTime(Date date) {
		return DateToString(date, DateStyle.HH_MM_SS);
	}

	/**
	 * 获取日期的星期。失败返回null。
	 * @param date 日期字符串
	 * @return 星期
	 */
	public static Week getWeek(String date) {
		Week week = null;
		DateStyle dateStyle = getDateStyle(date);
		if (dateStyle != null) {
			Date myDate = StringToDate(date, dateStyle);
			week = getWeek(myDate);
		}
		return week;
	}

	/**
	 * 获取日期的星期。失败返回null。
	 * @param date 日期
	 * @return 星期
	 */
	public static Week getWeek(Date date) {
		Week week = null;
		Calendar calendar = Calendar.getInstance();
		calendar.setTime(date);
		int weekNumber = calendar.get(Calendar.DAY_OF_WEEK) - 1;
		switch (weekNumber) {
		case 0:
			week = Week.SUNDAY;
			break;
		case 1:
			week = Week.MONDAY;
			break;
		case 2:
			week = Week.TUESDAY;
			break;
		case 3:
			week = Week.WEDNESDAY;
			break;
		case 4:
			week = Week.THURSDAY;
			break;
		case 5:
			week = Week.FRIDAY;
			break;
		case 6:
			week = Week.SATURDAY;
			break;
		}
		return week;
	}

	/**
	 * 获取两个日期相差的天数
	 * @param date 日期字符串
	 * @param otherDate 另一个日期字符串
	 * @return 相差天数。如果失败则返回-1
	 */
	public static int getIntervalDays(String date, String otherDate) {
		return getIntervalDays(StringToDate(date), StringToDate(otherDate));
	}

	/**
	 * @param date 日期
	 * @param otherDate 另一个日期
	 * @return 相差天数。如果失败则返回-1
	 */
	public static int getIntervalDays(Date date, Date otherDate) {
		int num = -1;
		Date dateTmp = DateUtil.StringToDate(DateUtil.getDate(date), DateStyle.YYYY_MM_DD);
		Date otherDateTmp = DateUtil.StringToDate(DateUtil.getDate(otherDate), DateStyle.YYYY_MM_DD);
		if (dateTmp != null && otherDateTmp != null) {
			long time = Math.abs(dateTmp.getTime() - otherDateTmp.getTime());
			num = (int) (time / (24 * 60 * 60 * 1000));
		}
		return num;
	}
}
```

### DateStyle类

```java

package com.itkt.mtravel.hotel.util;

public enum DateStyle {
	
	YYYY_MM("yyyy-MM", false),
	YYYY_MM_DD("yyyy-MM-dd", false),
	YYYY_MM_DD_HH_MM("yyyy-MM-dd HH:mm", false),
	YYYY_MM_DD_HH_MM_SS("yyyy-MM-dd HH:mm:ss", false),
	
	YYYY_MM_EN("yyyy/MM", false),
	YYYY_MM_DD_EN("yyyy/MM/dd", false),
	YYYY_MM_DD_HH_MM_EN("yyyy/MM/dd HH:mm", false),
	YYYY_MM_DD_HH_MM_SS_EN("yyyy/MM/dd HH:mm:ss", false),
	
	YYYY_MM_CN("yyyy年MM月", false),
	YYYY_MM_DD_CN("yyyy年MM月dd日", false),
	YYYY_MM_DD_HH_MM_CN("yyyy年MM月dd日 HH:mm", false),
	YYYY_MM_DD_HH_MM_SS_CN("yyyy年MM月dd日 HH:mm:ss", false),
	
	HH_MM("HH:mm", true),
	HH_MM_SS("HH:mm:ss", true),
	
	MM_DD("MM-dd", true),
	MM_DD_HH_MM("MM-dd HH:mm", true),
	MM_DD_HH_MM_SS("MM-dd HH:mm:ss", true),
	
	MM_DD_EN("MM/dd", true),
	MM_DD_HH_MM_EN("MM/dd HH:mm", true),
	MM_DD_HH_MM_SS_EN("MM/dd HH:mm:ss", true),
	
	MM_DD_CN("MM月dd日", true),
	MM_DD_HH_MM_CN("MM月dd日 HH:mm", true),
	MM_DD_HH_MM_SS_CN("MM月dd日 HH:mm:ss", true);
	
	private String value;
	
	private boolean isShowOnly;
	
	DateStyle(String value, boolean isShowOnly) {
		this.value = value;
		this.isShowOnly = isShowOnly;
	}
	
	public String getValue() {
		return value;
	}
	
	public boolean isShowOnly() {
		return isShowOnly;
	}
}

```
### Week类

```java

package com.util;

public enum Week {

	MONDAY("星期一", "Monday", "Mon.", 1),
	TUESDAY("星期二", "Tuesday", "Tues.", 2),
	WEDNESDAY("星期三", "Wednesday", "Wed.", 3),
	THURSDAY("星期四", "Thursday", "Thur.", 4),
	FRIDAY("星期五", "Friday", "Fri.", 5),
	SATURDAY("星期六", "Saturday", "Sat.", 6),
	SUNDAY("星期日", "Sunday", "Sun.", 7);
	
	String name_cn;
	String name_en;
	String name_enShort;
	int number;
	
	Week(String name_cn, String name_en, String name_enShort, int number) {
		this.name_cn = name_cn;
		this.name_en = name_en;
		this.name_enShort = name_enShort;
		this.number = number;
	}
	
	public String getChineseName() {
		return name_cn;
	}

	public String getName() {
		return name_en;
	}

	public String getShortName() {
		return name_enShort;
	}

	public int getNumber() {
		return number;
	}
}

```
### 添加日期风格（DateStyle）时需要注意的事项：

1. 不允许复的日期风格。例如：yyyy-MM-dd和yyyy-M-d，表现出的风格是相同的。只有当两个日期风格含有不同的字符时，才会看成是不相同的日期风格。例如：yyyy-MM-dd和yyyy-M-d EEE。当含有重复的日期风格时，可以通过isShowOnly=true来区分，isShowOnly=true表示该风格只是“格式化Date类型的日期”用，而不用作“自动判断String类型的日期”。

2. 日期必须含有完整年份信息。例如：MM-dd。没有年份的话，判断MM-dd是不准确的，因为无法识别出闰年（2-29）。其实MM-dd等类似的风格，我们日常习惯上，将其看作是“今年的M月d日”，而SimpleDateFormat中的parse方法中默认的年份为1970年。

3. 添加顺序为：由简到繁。目的在于2012-12和2012-12-1是等价的，虽然日期风格不一样，但默认会看成是一样的且以DateStyle匹配到的最后一个为主。因此最好将详细的日期风格写在后面。

## 优化版本三
该版本是一次较大的升级，农历相比公历复杂太多（真佩服古人的智慧），虽然有规律，但涉及到的取舍、近似的感念太多，况且本身的概念就已经很多了，我在网上也是查阅了很多的资料，虽然找到一些计算的方法，但都有些计算缺陷，后来才终于找到“寿天星文历”：一个十分精准的万年历。虽然它的功能十分强大，但相对的涉及到的计算也很多，逻辑和思路都相当的复杂了，维护成本很大，有时候项目中并不一定要用到这么强大的农历，因此该版本目前仅提供了农历的一些基本功能，在下一版本中，我会引入“寿天星文历”，以适合更多的大众需求。

源码：

### DateUtil类

新加入代码：

```java
	/**
	 * 获取简单农历对象
	 * @param date 日期字符串
	 * @return 简单农历对象
	 */
	public static SimpleLunarCalendar getSimpleLunarCalendar(String date) {
		return new SimpleLunarCalendar(DateUtil.StringToDate(date));
	}
	
	/**
	 * 获取简单农历对象
	 * @param date 日期
	 * @return 简单农历对象
	 */
	public static SimpleLunarCalendar getSimpleLunarCalendar(Date date) {
		return new SimpleLunarCalendar(date);
	}
```

### SimpleLunarCalendar类


```java

package com.util;

import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;

public class SimpleLunarCalendar {

	/** 最小时间1900-1-31*/
	private final static long minTimeInMillis = -2206425952001L;
	/** 最大时间2099-12-31 */
	private final static long maxTimeInMillis = 4102416000000L;
	/**
	 * 农历年数据表(1900-2099年)<br>
	 * <br>
	 * 每个农历年用16进制来表示，解析时转为2进制<br>
	 * 前12位分别表示12个农历月份的大小月，1是大月，0是小月<br>
	 * 最后4位表示闰月，转为十进制后即为闰月值，例如0110，则为闰6月
	 */
	private final static int[] lunarInfo = { 0x4bd8, 0x4ae0, 0xa570, 0x54d5, 0xd260, 0xd950, 0x5554, 0x56af, 0x9ad0, 0x55d2, 0x4ae0, 0xa5b6, 0xa4d0, 0xd250, 0xd295, 0xb54f, 0xd6a0, 0xada2, 0x95b0,
			0x4977, 0x497f, 0xa4b0, 0xb4b5, 0x6a50, 0x6d40, 0xab54, 0x2b6f, 0x9570, 0x52f2, 0x4970, 0x6566, 0xd4a0, 0xea50, 0x6a95, 0x5adf, 0x2b60, 0x86e3, 0x92ef, 0xc8d7, 0xc95f, 0xd4a0, 0xd8a6,
			0xb55f, 0x56a0, 0xa5b4, 0x25df, 0x92d0, 0xd2b2, 0xa950, 0xb557, 0x6ca0, 0xb550, 0x5355, 0x4daf, 0xa5b0, 0x4573, 0x52bf, 0xa9a8, 0xe950, 0x6aa0, 0xaea6, 0xab50, 0x4b60, 0xaae4, 0xa570,
			0x5260, 0xf263, 0xd950, 0x5b57, 0x56a0, 0x96d0, 0x4dd5, 0x4ad0, 0xa4d0, 0xd4d4, 0xd250, 0xd558, 0xb540, 0xb6a0, 0x95a6, 0x95bf, 0x49b0, 0xa974, 0xa4b0, 0xb27a, 0x6a50, 0x6d40, 0xaf46,
			0xab60, 0x9570, 0x4af5, 0x4970, 0x64b0, 0x74a3, 0xea50, 0x6b58, 0x5ac0, 0xab60, 0x96d5, 0x92e0, 0xc960, 0xd954, 0xd4a0, 0xda50, 0x7552, 0x56a0, 0xabb7, 0x25d0, 0x92d0, 0xcab5, 0xa950,
			0xb4a0, 0xbaa4, 0xad50, 0x55d9, 0x4ba0, 0xa5b0, 0x5176, 0x52bf, 0xa930, 0x7954, 0x6aa0, 0xad50, 0x5b52, 0x4b60, 0xa6e6, 0xa4e0, 0xd260, 0xea65, 0xd530, 0x5aa0, 0x76a3, 0x96d0, 0x4afb,
			0x4ad0, 0xa4d0, 0xd0b6, 0xd25f, 0xd520, 0xdd45, 0xb5a0, 0x56d0, 0x55b2, 0x49b0, 0xa577, 0xa4b0, 0xaa50, 0xb255, 0x6d2f, 0xada0, 0x4b63, 0x937f, 0x49f8, 0x4970, 0x64b0, 0x68a6, 0xea5f,
			0x6b20, 0xa6c4, 0xaaef, 0x92e0, 0xd2e3, 0xc960, 0xd557, 0xd4a0, 0xda50, 0x5d55, 0x56a0, 0xa6d0, 0x55d4, 0x52d0, 0xa9b8, 0xa950, 0xb4a0, 0xb6a6, 0xad50, 0x55a0, 0xaba4, 0xa5b0, 0x52b0,
			0xb273, 0x6930, 0x7337, 0x6aa0, 0xad50, 0x4b55, 0x4b6f, 0xa570, 0x54e4, 0xd260, 0xe968, 0xd520, 0xdaa0, 0x6aa6, 0x56df, 0x4ae0, 0xa9d4, 0xa4d0, 0xd150, 0xf252, 0xd520 };
	/** 十二生肖 */
	private final static String[] Animals = { "鼠", "牛", "虎", "兔", "龙", "蛇", "马", "羊", "猴", "鸡", "狗", "猪" };
	/** 农历中文字符串一 */
	private final static String[] lunarString1 = { "零", "一", "二", "三", "四", "五", "六", "七", "八", "九" };
	/** 农历中文字符串二 */
	private final static String[] lunarString2 = { "初", "十", "廿", "卅", "正", "腊", "冬", "闰" };
	/** 农历年 */
	private int lunarYear;
	/** 农历月 */
	private int lunarMonth;
	/** 农历日 */
	private int lunarDay;
	/** 是否是闰月 */
	private boolean isLeap;
	/** 是否是闰年 */
	private boolean isLeapYear;
	/** 某农历月的最大天数 */
	private int maxDayInMonth = 29;

	/**
	 * 通过 TimeInMillis 构建农历信息
	 * @param TimeInMillis
	 */
	public SimpleLunarCalendar(long TimeInMillis) {
		this.init(TimeInMillis);
	}

	/**
	 * 通过 Date 对象构建农历信息
	 * @param date 指定日期对象
	 */
	public SimpleLunarCalendar(Date date) {
		if (date == null)
			date = new Date();
		this.init(date.getTime());
	}

	/**
	 * 农历初始化
	 * @param timeInMillis 时间毫秒数
	 */
	private void init(long timeInMillis) {
		if (timeInMillis > minTimeInMillis && timeInMillis < maxTimeInMillis) {
			// 以农历为1900年正月一日的1900-1-31作为起始日期
			Calendar baseDate = new GregorianCalendar(1900, 0, 31);
			// 距离起始日期间隔的总天数
			long offset = (timeInMillis - baseDate.getTimeInMillis()) / 86400000;
			// 默认农历年为1900年，且由此开始推算农历年份
			this.lunarYear = 1900;
			int daysInLunarYear = SimpleLunarCalendar.getLunarYearDays(this.lunarYear);
			// 递减每个农历年的总天数，确定农历年份
			while (this.lunarYear < 2100 && offset >= daysInLunarYear) {
				offset -= daysInLunarYear;
				daysInLunarYear = SimpleLunarCalendar.getLunarYearDays(++this.lunarYear);
			}
			// 获取该农历年的闰月月份
			int leapMonth = SimpleLunarCalendar.getLunarLeapMonth(this.lunarYear);
			// 没有闰月则不是闰年
			this.isLeapYear = leapMonth > 0;

			// 默认农历月为正月，且由此开始推荐农历月
			int lunarMonth = 1;
			// 是否递减农历月
			boolean isDecrease = true;
			boolean isLeap = false;
			int daysInLunarMonth = 0;
			// 递减每个农历月的总天数，确定农历月份
			while (lunarMonth < 13 && offset > 0) {
				if (isLeap && !isDecrease) {
					// 该农历年闰月的总天数
					daysInLunarMonth = SimpleLunarCalendar.getLunarLeapDays(this.lunarYear);
					isDecrease = true;
				} else {
					// 该农历年正常农历月份的天数
					daysInLunarMonth = SimpleLunarCalendar.getLunarMonthDays(this.lunarYear, lunarMonth);
				}
				if (offset < daysInLunarMonth) {
					break;
				}
				offset -= daysInLunarMonth;

				// 如果农历月是闰月，则不递增农历月份
				if (leapMonth == lunarMonth && isLeap == false) {
					isDecrease = false;
					isLeap = true;
				} else {
					lunarMonth++;
				}
			}
			// 如果daysInLunarMonth为0则说明默认农历月即为返回的农历月
			this.maxDayInMonth = daysInLunarMonth != 0 ? daysInLunarMonth : SimpleLunarCalendar.getLunarMonthDays(this.lunarYear, lunarMonth);
			this.lunarMonth = lunarMonth;
			this.isLeap = (lunarMonth == leapMonth && isLeap);
			this.lunarDay = (int) offset + 1;
		}
	}

	/**
	 * 获取某农历年的总天数
	 * @param lunarYear 农历年份
	 * @return 该农历年的总天数
	 */
	private static int getLunarYearDays(int lunarYear) {
		// 按小月计算,农历年最少有12 * 29 = 348天
		int daysInLunarYear = 348;

		// 遍历前12位
		for (int i = 0x8000; i > 0x8; i >>= 1) {
			// 每个大月累加一天
			daysInLunarYear += ((SimpleLunarCalendar.lunarInfo[lunarYear - 1900] & i) != 0) ? 1 : 0;
		}
		// 加上闰月天数
		daysInLunarYear += SimpleLunarCalendar.getLunarLeapDays(lunarYear);

		return daysInLunarYear;
	}

	/**
	 * 获取某农历年闰月的总天数
	 * @param lunarYear 农历年份
	 * @return 该农历年闰月的总天数，没有闰月返回0
	 */
	private static int getLunarLeapDays(int lunarYear) {
		// 下一年最后4bit为1111,返回30(大月)
		// 下一年最后4bit不为1111,返回29(小月)
		// 若该年没有闰月,返回0
		return SimpleLunarCalendar.getLunarLeapMonth(lunarYear) > 0 ? ((SimpleLunarCalendar.lunarInfo[lunarYear - 1899] & 0xf) == 0xf ? 30 : 29) : 0;
	}

	/**
	 * 获取某农历年闰月月份
	 * @param lunarYear 农历年份
	 * @return 该农历年闰月的月份，没有闰月返回0
	 */
	private static int getLunarLeapMonth(int lunarYear) {
		// 匹配后4位
		int leapMonth = SimpleLunarCalendar.lunarInfo[lunarYear - 1900] & 0xf;
		// 若最后4位全为1或全为0,表示没闰
		leapMonth = (leapMonth == 0xf ? 0 : leapMonth);
		return leapMonth;
	}

	/**
	 * 获取某农历年某农历月份的总天数
	 * @param lunarYear 农历年份
	 * @param lunarMonth 农历月份
	 * @return 该农历年该农历月的总天数
	 */
	private static int getLunarMonthDays(int lunarYear, int lunarMonth) {
		// 匹配前12位代表的相应农历月份的大小月，大月30天，小月29天
		int daysInLunarMonth = ((SimpleLunarCalendar.lunarInfo[lunarYear - 1900] & (0x10000 >> lunarMonth)) != 0) ? 30 : 29;
		return daysInLunarMonth;
	}

	/**
	 * 返回指定数字的农历年份表示字符串
	 * @param lunarYear 农历年份(数字,0为甲子)
	 * @return 农历年份字符串
	 */
	private static String getLunarYearString(int lunarYear) {
		String lunarYearString = "";
		String year = String.valueOf(lunarYear);
		for (int i = 0; i < year.length(); i++) {
			char yearChar = year.charAt(i);
			int index = Integer.parseInt(String.valueOf(yearChar));
			lunarYearString += lunarString1[index];
		}
		return lunarYearString;
	}

	/**
	 * 返回指定数字的农历月份表示字符串
	 * @param lunarMonth 农历月份(数字)
	 * @return 农历月份字符串 (例:正)
	 */
	private static String getLunarMonthString(int lunarMonth) {
		String lunarMonthString = "";
		if (lunarMonth == 1) {
			lunarMonthString = SimpleLunarCalendar.lunarString2[4];
		} else {
			if (lunarMonth > 9)
				lunarMonthString += SimpleLunarCalendar.lunarString2[1];
			if (lunarMonth % 10 > 0)
				lunarMonthString += SimpleLunarCalendar.lunarString1[lunarMonth % 10];
		}
		return lunarMonthString;
	}

	/**
	 * 返回指定数字的农历日表示字符串
	 * @param lunarDay 农历日(数字)
	 * @return 农历日字符串 (例: 廿一)
	 */
	private static String getLunarDayString(int lunarDay) {
		if (lunarDay < 1 || lunarDay > 30)
			return "";
		int i1 = lunarDay / 10;
		int i2 = lunarDay % 10;
		String c1 = SimpleLunarCalendar.lunarString2[i1];
		String c2 = SimpleLunarCalendar.lunarString1[i2];
		if (lunarDay < 11)
			c1 = SimpleLunarCalendar.lunarString2[0];
		if (i2 == 0)
			c2 = SimpleLunarCalendar.lunarString2[1];
		return c1 + c2;
	}

	/**
	 * 取农历年生肖
	 * @return 农历年生肖(例:龙)
	 */
	public String getAnimalString() {
		if (lunarYear == 0)
			return null;
		return SimpleLunarCalendar.Animals[(this.lunarYear - 4) % 12];
	}

	/**
	 * 返回农历日期字符串
	 * @return 农历日期字符串
	 */
	public String getDayString() {
		if (lunarDay == 0)
			return null;
		return SimpleLunarCalendar.getLunarDayString(this.lunarDay);
	}

	/**
	 * 返回农历日期字符串
	 * @return 农历日期字符串
	 */
	public String getMonthString() {
		if (lunarMonth == 0)
			return null;
		return (this.isLeap() ? "闰" : "") + SimpleLunarCalendar.getLunarMonthString(this.lunarMonth);
	}

	/**
	 * 返回农历日期字符串
	 * @return 农历日期字符串
	 */
	public String getYearString() {
		if (lunarYear == 0)
			return null;
		return SimpleLunarCalendar.getLunarYearString(this.lunarYear);
	}

	/**
	 * 返回农历表示字符串
	 * @return 农历字符串(例:甲子年正月初三)
	 */
	public String getDateString() {
		if (lunarYear == 0)
			return null;
		return this.getYearString() + "年" + this.getMonthString() + "月" + this.getDayString() + "日";
	}

	/**
	 * 农历年是否是闰月
	 * @return 农历年是否是闰月
	 */
	public boolean isLeap() {
		return isLeap;
	}

	/**
	 * 农历年是否是闰年
	 * @return 农历年是否是闰年
	 */
	public boolean isLeapYear() {
		return isLeapYear;
	}

	/**
	 * 当前农历月是否是大月
	 * @return 当前农历月是大月
	 */
	public boolean isBigMonth() {
		return this.getMaxDayInMonth() > 29;
	}

	/**
	 * 当前农历月有多少天
	 * @return 天数
	 */
	public int getMaxDayInMonth() {
		if (lunarYear == 0)
			return 0;
		return this.maxDayInMonth;
	}

	/**
	 * 农历日期
	 * @return 农历日期
	 */
	public int getDay() {
		return lunarDay;
	}

	/**
	 * 农历月份
	 * @return 农历月份
	 */
	public int getMonth() {
		return lunarMonth;
	}

	/**
	 * 农历年份
	 * @return 农历年份
	 */
	public int getYear() {
		return lunarYear;
	}
}

```
### 说明：

该农历核心算法是从网上寻找而来，但我进行了完善，适用的年份为：1900年——2099年，我去掉了不精准的部分（如天干地支），目前该简单农历只能满足显示阴历信息，适合需要较为简单的用户适用。另外若想支持的年限范围更广，则需要修改lunarInfo（农历年数据表），并做适当的调整（默认农历年）即可。
