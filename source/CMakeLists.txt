# This file is part of HOGclassifier.

# HOGclassifier is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.

# HOGclassifier is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.

# You should have received a copy of the GNU General Public License
# along with HOGclassifier.  If not, see <http://www.gnu.org/licenses/>.

FIND_PACKAGE(OpenMP)
FIND_PACKAGE(OpenCV 2 REQUIRED)

if(OPENMP_FOUND)
 set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
 set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS} -D_PWN_USE_OPENMP_ -DEIGEN_DONT_PARALLELIZE")
 set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif(OPENMP_FOUND)

if(OPENCV_FOUND)
  message("opencv found")
endif(OPENCV_FOUND)

ADD_LIBRARY(logger_lib
	logger.h logger.cpp
)

ADD_LIBRARY(parser_lib
	directory_parser.h directory_parser.cpp
  lasers_parser.h lasers_parser.cpp
)

ADD_LIBRARY(temp_result_writer_lib
  temp_result_writer.h temp_result_writer.cpp
  depth_estimator.h depth_estimator.cpp
  bumblebee_camera_params.h
)

ADD_LIBRARY(svm_lib
	svm.h svm.cpp
	svm_binder.h svm_binder.cpp
)

ADD_LIBRARY(utils_lib
  utils.h
)

ADD_LIBRARY(sequence_generator_lib
  sequence_generator.h sequence_generator.cpp
)

ADD_LIBRARY(car_detection_lib
  car_detector.h car_detector.cpp
  feature_detector.h feature_detector.cpp
  feature_detector_cpu.h feature_detector_cpu.cpp
  feature_detector_gpu.h feature_detector_gpu.cpp
)

ADD_EXECUTABLE(train_and_predict main.cpp)

SET_TARGET_PROPERTIES(logger_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_logger_lib)
SET_TARGET_PROPERTIES(utils_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_utils_lib LINKER_LANGUAGE CXX)
SET_TARGET_PROPERTIES(temp_result_writer_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_temp_result_writer_lib)
SET_TARGET_PROPERTIES(parser_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_parser_lib)
SET_TARGET_PROPERTIES(svm_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_svm_lib)
SET_TARGET_PROPERTIES(car_detection_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_car_detection_lib)
SET_TARGET_PROPERTIES(sequence_generator_lib PROPERTIES OUTPUT_NAME ${LIB_PREFIX}_sequence_generator_lib)

SET_TARGET_PROPERTIES(train_and_predict PROPERTIES OUTPUT_NAME train_and_predict)

TARGET_LINK_LIBRARIES(svm_lib logger_lib)
TARGET_LINK_LIBRARIES(parser_lib logger_lib)
TARGET_LINK_LIBRARIES(temp_result_writer_lib utils_lib ${OpenCV_LIBS})
TARGET_LINK_LIBRARIES(car_detection_lib logger_lib svm_lib temp_result_writer_lib ${OpenCV_LIBS})
TARGET_LINK_LIBRARIES(train_and_predict parser_lib svm_lib car_detection_lib logger_lib temp_result_writer_lib sequence_generator_lib utils_lib ${OpenCV_LIBS})
