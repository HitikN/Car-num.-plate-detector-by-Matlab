# Car-num.-plate-detector-by-Matlab


img = imread('C:\Users\HITIK KUMAR NAYAK\Downloads\car number plate.jpg');


gray_img = rgb2gray(img);

enhanced_img = imadjust(gray_img);


filtered_img = medfilt2(enhanced_img, [3 3]);

edge_img = edge(filtered_img, 'Canny');


se = strel('rectangle', [5 5]);
dilated_img = imdilate(edge_img, se);

contours = bwconncomp(dilated_img);

stats = regionprops(contours, 'BoundingBox', 'Area');
min_area = 500; % Adjust this value based on the size of the number plate
bounding_boxes = [];
for i = 1:length(stats)
    if stats(i).Area > min_area
        bounding_boxes = [bounding_boxes; stats(i).BoundingBox];
    end
end

figure;
imshow(img);
hold on;
for i = 1:size(bounding_boxes, 1)
    rectangle('Position', bounding_boxes(i,:), 'EdgeColor', 'g', 'LineWidth', 2);
end
hold off;

if ~isempty(bounding_boxes)
    [~, idx] = max(bounding_boxes(:, 3) .* bounding_boxes(:, 4)); % largest area
    number_plate_img = imcrop(gray_img, bounding_boxes(idx, :));
    
    
    figure;
    imshow(number_plate_img);
    

    ocr_result = ocr(number_plate_img);
    number_plate_text = ocr_result.Text;

    disp('Detected Number Plate:');
    disp(strtrim(number_plate_text));
else
    disp('No number plate detected.');
end
