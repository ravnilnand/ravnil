## How to create Bitmap using IFrameCallback
I don't recommend this way because IFrameCallback is relatively slow but if you really need to create Bitmap in IFrameCallback, please refer following code.

``` java
    private final Bitmap bitmap = Bitmap.createBitmap(UVCCamera.DEFAULT_PREVIEW_WIDTH, UVCCamera.DEFAULT_PREVIEW_HEIGHT, Bitmap.Config.RGB_565);
    ...
    private final IFrameCallback mIFrameCallback = new IFrameCallback() {
    	@Override
    	public void onFrame(final ByteBuffer frame) {
    		frame.clear();
    		synchronized (bitmap) {
    			bitmap.copyPixelsFromBuffer(frame);
    		}
    		mImageView.post(mUpdateImageTask); // this is just for showing result
    	}
    };

    private final Runnable mUpdateImageTask = new Runnable() {
    	@Override
    	public void run() {
    		synchronized (bitmap) {
    			mImageView.setImageBitmap(bitmap);
     		}
    	}
    };
```  
