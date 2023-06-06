CTF Yarışmaları için tasasrladığım 2 video arasındaki farkı bulan python toolu.

    import cv2

    def find_video_difference(video1_path, video2_path, output_path):
        cap1 = cv2.VideoCapture(video1_path)
        cap2 = cv2.VideoCapture(video2_path)

        # Kontroller
        if not cap1.isOpened() or not cap2.isOpened():
            print("Hata: Videolar açılamıyor.")
            return

        # Videoların çözünürlüğünü al
        frame_width = int(cap1.get(3))
        frame_height = int(cap1.get(4))
        output = cv2.VideoWriter(output_path, cv2.VideoWriter_fourcc(*'mp4v'), 30, (frame_width, frame_height))

        while cap1.isOpened() and cap2.isOpened():
            ret1, frame1 = cap1.read()
            ret2, frame2 = cap2.read()

            if not ret1 or not ret2:
                break

            # Kareleri gri tonlamaya dönüştür
            gray1 = cv2.cvtColor(frame1, cv2.COLOR_BGR2GRAY)
            gray2 = cv2.cvtColor(frame2, cv2.COLOR_BGR2GRAY)

            # Kareler arasındaki farkı hesapla
            diff = cv2.absdiff(gray1, gray2)

            # Farkı göstermek için threshold uygula
            _, thresh = cv2.threshold(diff, 30, 255, cv2.THRESH_BINARY)

            # Farkı görüntüye ekle
            output_frame = cv2.cvtColor(thresh, cv2.COLOR_GRAY2BGR)
            output.write(output_frame)

            # Gösterim
            cv2.imshow('Difference', output_frame)
            if cv2.waitKey(1) & 0xFF == ord('q'):
                break

        # Kaynakları serbest bırak
        cap1.release()
        cap2.release()
        output.release()
        cv2.destroyAllWindows()

        print("Video farkı bulma tamamlandı. Sonuç videosu:", output_path)

    # Kullanım örneği
    video1_path = "video1.mp4"
    video2_path = "video2.mp4"
    output_path = "video_difference.mp4"

    find_video_difference(video1_path, video2_path, output_path)
