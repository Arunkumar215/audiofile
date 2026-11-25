# Ex.No:3 Develop a simple application to play and control the audio file in android studio.


## AIM:

To develop a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as audiofile and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml and create start,pause and stop button.

Step 6: Display message give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to play and control the audio file”.
Developed by: Arunkumar S A
Registeration Number : 212223220009
*/
```

### MainActivity.java:
```
package com.example.ex4;

import android.media.MediaPlayer;
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private MediaPlayer mediaPlayer;
    private Button btnPlay, btnPause, btnStop;
    private SeekBar seekBar;
    private TextView tvCurrentTime, tvTotalTime;

    private final Handler handler = new Handler(Looper.getMainLooper());
    private Runnable updateSeekBarRunnable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnPlay = findViewById(R.id.btnPlay);
        btnPause = findViewById(R.id.btnPause);
        btnStop = findViewById(R.id.btnStop);
        seekBar = findViewById(R.id.seekBar);
        tvCurrentTime = findViewById(R.id.tvCurrentTime);
        tvTotalTime = findViewById(R.id.tvTotalTime);

        // Initialize MediaPlayer
        initMediaPlayer();

        // Runnable to update seekbar progress and current time label during playback
        updateSeekBarRunnable = new Runnable() {
            @Override
            public void run() {
                if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                    int currentPosition = mediaPlayer.getCurrentPosition();
                    seekBar.setProgress(currentPosition);
                    tvCurrentTime.setText(formatTime(currentPosition));
                    handler.postDelayed(this, 200);
                }
            }
        };

        // Play button listener
        btnPlay.setOnClickListener(v -> {
            if (mediaPlayer == null) {
                initMediaPlayer();
            }
            if (mediaPlayer != null) {
                // If track is near the end or finished, rewind to start
                if (mediaPlayer.getCurrentPosition() >= mediaPlayer.getDuration() - 500) {
                    mediaPlayer.seekTo(0);
                }
                if (!mediaPlayer.isPlaying()) {
                    mediaPlayer.start();
                    handler.removeCallbacks(updateSeekBarRunnable);
                    handler.post(updateSeekBarRunnable);
                    Toast.makeText(MainActivity.this, "Playing Audio", Toast.LENGTH_SHORT).show();
                }
            }
        });

        // Pause button listener
        btnPause.setOnClickListener(v -> {
            if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                mediaPlayer.pause();
                handler.removeCallbacks(updateSeekBarRunnable);
                Toast.makeText(MainActivity.this, "Audio Paused", Toast.LENGTH_SHORT).show();
            }
        });

        // Stop button listener
        btnStop.setOnClickListener(v -> {
            stopAudio();
            Toast.makeText(MainActivity.this, "Audio Stopped", Toast.LENGTH_SHORT).show();
        });

        // SeekBar listener for scrubbing/seeking
        seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                if (fromUser) {
                    if (mediaPlayer != null) {
                        mediaPlayer.seekTo(progress);
                    }
                    tvCurrentTime.setText(formatTime(progress));
                }
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                handler.removeCallbacks(updateSeekBarRunnable);
            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                    handler.post(updateSeekBarRunnable);
                }
            }
        });
    }

    private void initMediaPlayer() {
        mediaPlayer = MediaPlayer.create(this, R.raw.audio);
        if (mediaPlayer != null) {
            int duration = mediaPlayer.getDuration();
            seekBar.setMax(duration);
            seekBar.setProgress(0);
            tvTotalTime.setText(formatTime(duration));
            tvCurrentTime.setText(formatTime(0));

            mediaPlayer.setOnCompletionListener(mp -> {
                handler.removeCallbacks(updateSeekBarRunnable);
                mediaPlayer.seekTo(0);
                seekBar.setProgress(0);
                tvCurrentTime.setText(formatTime(0));
                Toast.makeText(MainActivity.this, "Playback Completed", Toast.LENGTH_SHORT).show();
            });
        }
    }

    private void stopAudio() {
        if (mediaPlayer != null) {
            handler.removeCallbacks(updateSeekBarRunnable);
            if (mediaPlayer.isPlaying()) {
                mediaPlayer.pause();
            }
            mediaPlayer.seekTo(0);
        }
        seekBar.setProgress(0);
        tvCurrentTime.setText(formatTime(0));
    }

    private void releaseMediaPlayer() {
        if (mediaPlayer != null) {
            handler.removeCallbacks(updateSeekBarRunnable);
            try {
                if (mediaPlayer.isPlaying()) {
                    mediaPlayer.stop();
                }
                mediaPlayer.release();
            } catch (Exception ignored) {
            }
            mediaPlayer = null;
        }
    }

    private String formatTime(int milliseconds) {
        int seconds = (milliseconds / 1000) % 60;
        int minutes = (milliseconds / (1000 * 60)) % 60;
        return String.format(Locale.getDefault(), "%02d:%02d", minutes, seconds);
    }

    @Override
    protected void onPause() {
        super.onPause();
        if (mediaPlayer != null && mediaPlayer.isPlaying()) {
            mediaPlayer.pause();
            handler.removeCallbacks(updateSeekBarRunnable);
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        releaseMediaPlayer();
    }
}
```

### main_activity.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true"
    android:background="?android:attr/colorBackground">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center"
        android:padding="24dp">

        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:cardCornerRadius="16dp"
            app:cardElevation="6dp"
            app:cardUseCompatPadding="true">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:gravity="center_horizontal"
                android:padding="24dp">

                <!-- Title & Subtitle -->
                <TextView
                    android:id="@+id/tvTrackTitle"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Audio Player"
                    android:textSize="24sp"
                    android:textStyle="bold"
                    android:textColor="?attr/colorOnSurface" />

                <TextView
                    android:id="@+id/tvTrackSubtitle"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="res/raw/audio.mpeg"
                    android:textSize="14sp"
                    android:textColor="?android:attr/textColorSecondary"
                    android:layout_marginTop="4dp"
                    android:layout_marginBottom="24dp" />

                <!-- Timeline SeekBar -->
                <SeekBar
                    android:id="@+id/seekBar"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginHorizontal="8dp" />

                <!-- Time Labels -->
                <RelativeLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:paddingHorizontal="16dp"
                    android:layout_marginTop="4dp"
                    android:layout_marginBottom="24dp">

                    <TextView
                        android:id="@+id/tvCurrentTime"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentStart="true"
                        android:text="00:00"
                        android:textSize="12sp"
                        android:textColor="?android:attr/textColorSecondary" />

                    <TextView
                        android:id="@+id/tvTotalTime"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentEnd="true"
                        android:text="00:00"
                        android:textSize="12sp"
                        android:textColor="?android:attr/textColorSecondary" />
                </RelativeLayout>

                <!-- Control Buttons -->
                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:gravity="center">

                    <Button
                        android:id="@+id/btnPlay"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Play"
                        android:layout_marginEnd="8dp" />

                    <Button
                        android:id="@+id/btnPause"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Pause"
                        android:layout_marginEnd="8dp" />

                    <Button
                        android:id="@+id/btnStop"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Stop" />

                </LinearLayout>

            </LinearLayout>

        </com.google.android.material.card.MaterialCardView>

    </LinearLayout>

</ScrollView>

```
## OUTPUT

<img width="1917" height="957" alt="Screenshot 2026-11-23 092357" src="https://github.com/user-attachments/assets/ac6e92ea-679e-4929-9c98-81ff35745a12" />



## RESULT
Thus a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio is developed and executed successfully.
