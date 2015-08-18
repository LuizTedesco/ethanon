#Codigo verificador de plataforma
if(WIN32)
	
	# Adiciona flags de compilacao
	add_definitions(-DWIN32 -DNDEBUG -D_CONSOLE -D_CRT_SECURE_NO_WARNINGS)

	set(msvc9Files
		${PROJECT_SOURCE_DIR}/Source/projects/msvc9/afxres.h
		${PROJECT_SOURCE_DIR}/Source/projects/msvc9/resource.h
		#${PROJECT_SOURCE_DIR}/Source/projects/msvc9/file_version.rc
		${PROJECT_SOURCE_DIR}/Source/projects/msvc9/resource.rc
	)

	set(msvc9LostFile
		main.cpp
	)
	
	include_directories(			
		"${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/BoostSDK/"	
		"${PROJECT_SOURCE_DIR}/Source/src/box2d/"	
		"${PROJECT_SOURCE_DIR}/Source/src/gs2d/src/"	
		"${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/DX9SDK/Lib/"
		"${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/CgSDK/lib/"
	)

	# Cria o Executavel chamado Ethanon_Machine para Windows 32
	add_executable(Ethanon_Machine WIN32 EXCLUDE_FROM_ALL ${msvc9Files} ${msvc9LostFile})

	#Liga o executavel a algumas Libs que ele precisa para funcionar corretamente
	target_link_libraries(Ethanon_Machine 
		winmm
		${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/DX9SDK/Lib/strmiids.lib
		${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/DX9SDK/Lib/d3d9.lib 
		${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/DX9SDK/Lib/d3dx9.lib 
		${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/CgSDK/lib/cgD3D9.lib 
		${PROJECT_SOURCE_DIR}/Source/src/gs2d/vendors/CgSDK/lib/cg.lib		
		${PROJECT_SOURCE_DIR}/Source/libboost_date_time-vc90-mt-sgd-1_57.lib		
	)

	#Liga o executavel aos outros modulos do projeto
	target_link_libraries(Ethanon_Machine 
		angelscript
		audiere
		Box2D
		Ethanon_Engine
		GS2D
		hashlib
		libzip
		Platform
		zlib
	)
	
	# Cria formatacao de pastas no VS
	source_group(Resource_Files FILES ${msvc9Files})
	source_group(${msvc9LostFile})


#Codigo verificador de plataforma
elseif(UNIX)

	set(main
		main.cpp
	)

	IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
		#Busca no sistema os FrameWorks em questao e nomeia bibliotecas com esses frameworks
		find_library( COREDATA_LIBRARY CoreData)
		find_library( COCOA_LIBRARY Cocoa)
		find_library( FOUNDATION_LIBRARY Foundation)
		find_library( CG_LIBRARY Cg)
		find_library( SDL2_LIBRARY SDL2)
		find_library( APPKIT_LIBRARY AppKit)
		find_library( OPENGL_LIBRARY OpenGl)
		find_library( AVFoundation_LIBRARY AVFoundation)
		find_library( AudioToolbox_LIBRARY AudioToolbox)
		find_library( OpenAL_LIBRARY OpenAL)

		# Busca no sistema o caminho para AppKit/AppKit.h
		find_path(APPKIT_INCLUDE_DIRS AppKit/AppKit.h)

		# Adiciona o caminho para AppKit/AppKit.h ao caminho de busca do projeto
		include_directories(${APPKIT_INCLUDE_DIRS})

		#Cria o executavel Ethanon_Machine para MacOsX
		add_executable(Ethanon_Machine  ${main})
		
		#Liga o executavel aos frameworks necessarios e aos outros modulos do projeto
		target_link_libraries (Ethanon_Machine 
			${APPKIT_LIBRARY}
			${COREDATA_LIBRARY}
			${COCOA_LIBRARY}
			${FOUNDATION_LIBRARY}
			${OPENGL_LIBRARY}
			${CG_LIBRARY}
			${SDL2_LIBRARY}
			${AVFoundation_LIBRARY}
			${AudioToolbox_LIBRARY}
			${OpenAL_LIBRARY}
			angelscript
			hashlib
			Platform
			Soil
			GS2D
			Box2D
			Ethanon_Engine
		)

	else()

		find_package(OpenGL REQUIRED)
		include_directories(${OpenGL_INCLUDE_DIRS})
		link_directories(${OpenGL_LIBRARY_DIRS})
		add_definitions(${OpenGL_DEFINITIONS})

								#set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/Utilites/cmake-modules)
		set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/Utilites/cmake-modules/")


		# sdl2
		INCLUDE(${CMAKE_SOURCE_DIR}/Utilites/cmake-modules/FindSDL2.cmake)

		find_package(SDL2 REQUIRED)
		include_directories(${SDL2_INCLUDE_DIR})
		#link_directories(${SDL2_LIBRARY_DIRS})
		add_definitions(${SDL2_DEFINITIONS})


		# Cg
		INCLUDE(${CMAKE_SOURCE_DIR}/Utilites/cmake-modules/FindCg.cmake)
		find_package(Cg REQUIRED)

		include_directories(${CG_INCLUDE_DIR})
		include_directories(${CG_Gl_INCLUDE_DIR})

		#link_directories(${CG_LIBRARY_DIRS})
		add_definitions(${CG_DEFINITIONS})
		add_definitions(${CG_GL_DEFINITIONS})

		#include_directories(${CG_Gl_INCLUDE_DIR})
		


		#find_package(SDL2 REQUIRED)
		#include_directories(${SDL2_INCLUDE_DIR})

		#find_package(Cg REQUIRED)
		#include_directories(${CG_INCLUDE_DIR})
		#include_directories(${CG_Gl_INCLUDE_DIR})


		#Cria o executavel Ethanon_Machine para MacOsX
		add_executable(Ethanon_Machine  ${main})
		
		#Liga o executavel aos frameworks necessarios e aos outros modulos do projeto
		target_link_libraries (Ethanon_Machine 
			Platform
			Ethanon_Engine
			hashlib
			angelscript
			Box2D
			GS2D
			Soil
			Platform
			

			${OPENGL_LIBRARY}
			${CG_LIBRARY}
			${CG_GL_LIBRARY}
			${SDL2_LIBRARY}

		)
	endif()
	
#Fim da verificacao de Plataformas	
endif()