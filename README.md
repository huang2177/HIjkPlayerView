引入
1.将下面代码添加到项目的根目录的build.gradle：
allprojects {
	repositories {
		...
		maven { url 'https://jitpack.io' }
	}
}
  
1.将下面代码添加到app目录的build.gradle：
dependencies {
        compile 'com.github.huangbryant210707:HIjkPlayerView:1.0.0'
}

使用
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mPlayerView = (HIjkPlayerView) findViewById(R.id.player_view);
        
        //视频未播放时的封面图片
        Glide.with(this).load(IMAGE_URL).fitCenter().into(mPlayerView.mPlayerThumb);
        mPlayerView.init()
                .setTitle("title") //视频的的标题 
                .setSkipTip(1000*60*1) 
                .enableDanmaku()
                //.setDanmakuSource(getResources().openRawResource(R.raw.bili))
                .setVideoSource(null, VIDEO_URL, VIDEO_HD_URL, null, null)
                .setMediaQuality(HIjkPlayerView.MEDIA_QUALITY_HIGH);

        mIvSend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mPlayerView.sendDanmaku(mEditText.getText().toString(), false);
                mEditText.setText("");
                _closeSoftInput();
            }
        });
        mEditText.setOnFocusChangeListener(new View.OnFocusChangeListener() {
            @Override
            public void onFocusChange(View view, boolean isFocus) {
                if (isFocus) {
                    mPlayerView.editVideo();
                }
                mIsFocus = isFocus;
            }
           
        });
    }

    @Override
    protected void onResume() {
        super.onResume();
        mPlayerView.onResume();
    }

    @Override
    protected void onPause() {
        super.onPause();
        mPlayerView.onPause();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mPlayerView.onDestroy();
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        mPlayerView.configurationChanged(newConfig);
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (mPlayerView.handleVolumeKey(keyCode)) {
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }

    @Override
    public void onBackPressed() {
        if (mPlayerView.onBackPressed()) {
            return;
        }
        super.onBackPressed();
    }



    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        View view = getCurrentFocus();
        if (_isHideSoftInput(view, (int) ev.getX(), (int) ev.getY())) {
            _closeSoftInput();
            return true;
        }
        return super.dispatchTouchEvent(ev);
    }

    private void _closeSoftInput() {
        mEditText.clearFocus();
        SoftInputUtils.closeSoftInput(this);
        mPlayerView.recoverFromEditVideo();
    }

    private boolean _isHideSoftInput(View view, int x, int y) {
        if (view == null || !(view instanceof EditText) || !mIsFocus) {
            return false;
        }
        return x < mEtLayout.getLeft() ||
                x > mEtLayout.getRight() ||
                y < mEtLayout.getTop();
    }
