# ...existing code...
import os
import sys
import piexif

def copy_comment_to_title(directory):
    for filename in os.listdir(directory):
        if not filename.lower().endswith(('.jpg', '.jpeg')):
            continue
        filepath = os.path.join(directory, filename)
        if os.path.isfile(filepath):
            try:
                exif_dict = piexif.load(filepath)
                comment = exif_dict["0th"].get(piexif.ImageIFD.XPComment)
                if comment:
                    exif_dict["0th"][piexif.ImageIFD.XPTitle] = comment
                    exif_bytes = piexif.dump(exif_dict)
                    piexif.insert(exif_bytes, filepath)
            except Exception:
                pass

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python copy_comment_to_title.py <directory>")
        sys.exit(1)
    copy_comment_to_title(sys.argv[1])
# ...existing code...
