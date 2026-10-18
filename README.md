from pathlib import Path


def format_size(size_bytes):
    units = ["B", "KB", "MB", "GB", "TB"]
    size = float(size_bytes)

    for unit in units:
        if size < 1024 or unit == units[-1]:
            return f"{size:.2f} {unit}"
        size /= 1024

    return f"{size_bytes} B"


def calculate_folder_size(folder_path):
    folder = Path(folder_path).expanduser()

    if not folder.is_dir():
        raise ValueError("The specified folder does not exist")

    total_size = 0
    file_count = 0
    errors = []

    for path in folder.rglob("*"):
        try:
            if path.is_file():
                total_size += path.stat().st_size
                file_count += 1
        except OSError as error:
            errors.append(f"{path}: {error}")

    return total_size, file_count, errors


if __name__ == "__main__":
    folder_path = input("Enter folder path: ").strip()

    try:
        size, count, errors = calculate_folder_size(folder_path)

        print(f"Files found: {count}")
        print(f"Total size: {format_size(size)}")

        if errors:
            print(f"Warning: {len(errors)} file(s) could not be read.")

    except ValueError as error:
        print("Error:", error)
