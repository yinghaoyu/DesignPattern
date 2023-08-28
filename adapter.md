# 适配器模式

适配器模式是一种**结构型**模式。

举个例子，假设有一个播放器，需要根据不同的格式及对应的文件来播放，接口设计如下：

```cpp
class MediaPlayer
{
  virtual ~MediaPlayer();
  virtual void play(string type, string filename);
};
```

不同类型的播放器只需要实现这个接口即可，比如我们有一个 `ClassicMediaPlayer`，这个只能播放 `mp3` 类型的文件

```cpp
class ClassicMediaPlayer : public MediaPlayer
{
 public:
  void play(string type, string filename) override
  {
    if(type == "mp3")
    {
      cout << "play mp3" << endl;
    }
    else
    {
      cout << "no support format" << endl;
    }
  }
};
```

如果我想扩展，希望这个播放器可以播放更多种类，我们可以增加一个适配器：

```cpp
// 新增接口类
class AdvanceMediaPlayer
{
 public:
  virtual ~AdvanceMediaPlayer();
  virtual void playMP4(string filename);
  virtual void playAVI(string filename);
};

class AVIPlayer : public AdvanceMediaPlayer
{
 public:
 void playMP4(string filename) override
 {
 }

 void playAVI(string filename) override
 {
   cout << "play avi" << endl;
 }
}

class MP4Player : public AdvanceMediaPlayer
{
  void playMP4(string filename) override
  {
    cout << "play mp4" << endl;
  }

  void playAVI(string filename) override
  {
  }
};

class PlayerAdapter : public MediaPlayer
{
 private:
  AdvanceMediaPlayer* m_adp;

 public:
  PlayerAdapter(string type)
  {
    // 根据类型创建不同的播放器
    if(type == "mp4")
    {
      m_adp = new MP4Player();
    }
    else if(type == "AVI")
    {
      m_adp = new AVIPlayer();
    }
  }

  void play(string type, string filename)
  {
    // 根据类型播放
    if(type == "mp4")
    {
      m_adp->playMP4(filename);
    }
    else if(type == "AVI")
    {
      m_adp->playAVI(filename);
    }
    else
    {
      (new ClassicMediaPlayer())->play(type, filename);
    }
  }
};

class ExtendMediaPlayer : public MediaPlayer
{
 private:
  // 保存一个适配器
  PlayerAdapter* m_adp;
 public:
  void play(string type, string filename) override
  {
    m_adp = new PlayerAdapter(type);
    m_adp->play(type, filename);
  }
};

int main()
{
  MediaPlayer* audioPlayer = new ExtendMediaPlayer();

  audioPlayer->play("mp3", "love story.mp3");
  audioPlayer->play("mp4", "one piece.mp4");
  audioPlayer->play("avi", "tom an jerry.avi");
  return 0;
}
```
