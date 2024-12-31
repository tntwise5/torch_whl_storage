# ...existing code...
import os
import sys
import piexif
import pyexiv2

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
                    comment = bytes(comment)
                    exif_dict["0th"][piexif.ImageIFD.ImageDescription] = comment.decode('utf-16') if isinstance(comment, bytes) else str(comment)
                    exif_bytes = piexif.dump(exif_dict)
                    piexif.insert(exif_bytes, filepath)
                    
                    content = comment.decode('utf-16') if isinstance(comment, bytes) else str(comment)
                    metadata = pyexiv2.Image(filepath)
                    xmp_data = metadata.read_xmp()
                    xmp_data["Xmp.dc.title"] = content
                    xmp_data["Xmp.dc.description"] = content
                    metadata.modify_xmp(xmp_data)
                
            except Exception as e:
                print(e)

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python copy_comment_to_title.py <directory>")
        sys.exit(1)
    copy_comment_to_title(sys.argv[1])
# ...existing code...
